# trabalho-do-hiran1
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Folha de Pagamento Interativa</title>
    
    <style>
        /* INÍCIO DO CSS */
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f4f7f6;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            min-height: 100vh;
        }

        .container {
            background-color: #ffffff;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 6px 15px rgba(0, 0, 0, 0.15);
            width: 100%;
            max-width: 750px;
        }

        h1 {
            color: #1e3a8a;
            text-align: center;
            margin-bottom: 30px;
            border-bottom: 3px solid #3b82f6;
            padding-bottom: 15px;
        }

        .card {
            background-color: #eff6ff;
            border: 1px solid #bfdbfe;
            border-radius: 8px;
            padding: 20px;
            margin-bottom: 25px;
        }

        h2 {
            color: #1e3a8a;
            border-bottom: 1px solid #3b82f6;
            padding-bottom: 5px;
            margin-top: 0;
        }

        label {
            display: block;
            margin-top: 10px;
            font-weight: 600;
            color: #374151;
        }

        input[type="text"], input[type="number"] {
            width: 95%;
            padding: 12px;
            margin-top: 5px;
            margin-bottom: 15px;
            border: 1px solid #ccc;
            border-radius: 6px;
            box-sizing: border-box;
            transition: border-color 0.3s, box-shadow 0.3s;
        }

        input[type="number"]:focus, input[type="text"]:focus {
            border-color: #3b82f6;
            box-shadow: 0 0 5px rgba(59, 130, 246, 0.5);
            outline: none;
        }

        button {
            display: block;
            width: 100%;
            padding: 15px;
            background-color: #3b82f6;
            color: white;
            border: none;
            border-radius: 6px;
            font-size: 1.1em;
            font-weight: bold;
            cursor: pointer;
            transition: background-color 0.3s;
        }

        button:hover {
            background-color: #2563eb;
        }

        /* Estilo do Relatório (Holerite) */
        #relatorio {
            margin-top: 30px;
            border: 2px solid #10b981;
            background-color: #f0fdf4;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
        }

        th, td {
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid #d1d5db;
        }

        th {
            background-color: #a7f3d0;
            font-weight: bold;
            color: #065f46;
        }

        .positivo { color: #059669; font-weight: bold; } 
        .negativo { color: #ef4444; font-weight: bold; } 
        
        .destaque { background-color: #fde68a; font-weight: bold; }
        .destaque-final { background-color: #10b981; color: white; font-weight: bold; font-size: 1.3em; }

        .hidden {
            display: none;
        }
        /* FIM DO CSS */
    </style>
</head>
<body>
    <div class="container">
        <h1>💲 Controle de Folha de Pagamento</h1>
        
        <form id="payrollForm">
            <section class="card">
                <h2>Dados Iniciais</h2>
                <label for="nome">Nome do Funcionário:</label>
                <input type="text" id="nome" required placeholder="Ex: João da Silva">

                <label for="salarioBase">Salário Base (R$):</label>
                <input type="number" id="salarioBase" min="0" step="0.01" required placeholder="Ex: 3000.00">
            </section>

            <section class="card">
                <h2>Adicionais e Benefícios (Proventos)</h2>
                <label for="horasExtras">Horas Extras (R$):</label>
                <input type="number" id="horasExtras" min="0" value="0" step="0.01">

                <label for="beneficios">Outros Benefícios/Comissões (R$):</label>
                <input type="number" id="beneficios" min="0" value="0" step="0.01">
            </section>

            <section class="card">
                <h2>Outros Descontos</h2>
                <label for="descontos">Convênio Médico, Faltas, etc. (R$):</label>
                <input type="number" id="descontos" min="0" value="0" step="0.01">
                
                <small>Nota: INSS e IRRF são calculados automaticamente e são **SIMULADOS**.</small>
            </section>

            <button type="submit" id="calcularBtn">Calcular Folha de Pagamento</button>
        </form>

        <section id="relatorio" class="card hidden">
            <h2>Relatório de Pagamento (Holerite)</h2>
            <p><strong>Funcionário:</strong> <span id="relatorioNome"></span></p>
            <hr>
            <table>
                <thead>
                    <tr>
                        <th>Descrição</th>
                        <th>Valor (R$)</th>
                    </tr>
                </thead>
                <tbody>
                    <tr><td>Salário Base</td><td id="base"></td></tr>
                    <tr><td>Total de Adicionais</td><td id="adicionais" class="positivo"></td></tr>
                    <tr><td><strong>Salário Bruto</strong></td><td id="bruto" class="destaque"></td></tr>
                    <tr><td>(-) INSS (Simulado)</td><td id="inss" class="negativo"></td></tr>
                    <tr><td>(-) IRRF (Simulado)</td><td id="irrf" class="negativo"></td></tr>
                    <tr><td>(-) Outros Descontos</td><td id="outrosDescontos" class="negativo"></td></tr>
                    <tr><td><strong>Total de Descontos</strong></td><td id="totalDescontos" class="negativo"></td></tr>
                    <tr><td colspan="2" style="padding: 0;"></td></tr>
                    <tr><td><strong>SALÁRIO LÍQUIDO</strong></td><td id="liquido" class="destaque-final"></td></tr>
                </tbody>
            </table>
        </section>
        

    </div>
    
    <script>
        // INÍCIO DO JAVASCRIPT
        document.getElementById('payrollForm').addEventListener('submit', function(event) {
            event.preventDefault();

            // 1. Coleta de Dados
            const nome = document.getElementById('nome').value;
            const salarioBase = parseFloat(document.getElementById('salarioBase').value) || 0;
            const horasExtras = parseFloat(document.getElementById('horasExtras').value) || 0;
            const beneficios = parseFloat(document.getElementById('beneficios').value) || 0;
            const outrosDescontos = parseFloat(document.getElementById('descontos').value) || 0;

            // 2. Cálculos (Simulados - Simplificados para demonstração)
            
            // A. Salário Bruto
            const totalAdicionais = horasExtras + beneficios;
            const salarioBruto = salarioBase + totalAdicionais;

            // B. Descontos de Encargos (SIMULADO)
            const aliquotaINSS = 0.10; // 10% fixo simulado
            const inss = salarioBase * aliquotaINSS; 
            
            // Base de cálculo do IRRF (Salário Bruto - INSS)
            const baseIrrf = salarioBruto - inss;
            
            // Simulação do IRRF (15% sobre a base, se for > R$ 2000,00)
            let irrf = 0;
            if (baseIrrf > 2000) {
                const aliquotaIRRF = 0.15;
                irrf = baseIrrf * aliquotaIRRF;
            }

            // C. Total de Descontos
            const totalDescontos = inss + irrf + outrosDescontos;

            // D. Salário Líquido
            const salarioLiquido = salarioBruto - totalDescontos;

            // 3. Geração do Relatório (Holerite)

            // Função para formatar o valor como moeda brasileira (R$ 1.234,56)
            const formatarMoeda = (valor) => valor.toLocaleString('pt-BR', { minimumFractionDigits: 2, maximumFractionDigits: 2 });

            document.getElementById('relatorioNome').textContent = nome;
            document.getElementById('base').textContent = formatarMoeda(salarioBase);
            document.getElementById('adicionais').textContent = formatarMoeda(totalAdicionais);
            document.getElementById('bruto').textContent = formatarMoeda(salarioBruto);
            
            document.getElementById('inss').textContent = `(${formatarMoeda(inss)})`; 
            document.getElementById('irrf').textContent = `(${formatarMoeda(irrf)})`; 
            document.getElementById('outrosDescontos').textContent = `(${formatarMoeda(outrosDescontos)})`; 
            document.getElementById('totalDescontos').textContent = `(${formatarMoeda(totalDescontos)})`;
            
            document.getElementById('liquido').textContent = `R$ ${formatarMoeda(salarioLiquido)}`;

            // Exibe o relatório e rola a tela para ele
            const relatorio = document.getElementById('relatorio');
            relatorio.classList.remove('hidden');
            relatorio.scrollIntoView({ behavior: 'smooth' });
        });
        // FIM DO JAVASCRIPT
    </script>
</body>
</html>
