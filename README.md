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
