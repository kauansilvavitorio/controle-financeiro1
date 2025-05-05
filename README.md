<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Controle Financeiro Local</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 30px;
      background-color: #1e1e2f;
      color: #f0f0f0;
    }
    h1 {
      text-align: center;
      color: #00d1b2;
    }
    form, table {
      max-width: 700px;
      margin: 20px auto;
    }
    input, select, button {
      padding: 10px;
      margin: 5px;
      width: calc(100% - 22px);
      border: 1px solid #333;
      border-radius: 5px;
      background-color: #2e2e3f;
      color: #f0f0f0;
    }
    button {
      background-color: #00d1b2;
      border: none;
      color: #fff;
      cursor: pointer;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
      background: #2e2e3f;
      box-shadow: 0 0 10px #000;
    }
    th, td {
      padding: 10px;
      text-align: center;
      border: 1px solid #444;
    }
    th {
      background-color: #00d1b2;
      color: #fff;
    }
    #saldo {
      text-align: center;
      font-size: 1.4em;
      font-weight: bold;
      margin-top: 20px;
      color: #00d1b2;
    }
    .btn-excluir {
      background-color: #ff3860;
      color: white;
      border: none;
      padding: 5px 10px;
      cursor: pointer;
      border-radius: 4px;
    }
  </style>
</head>
<body>
  <h1>Controle Financeiro</h1>

  <form id="form-transacao">
    <input type="text" id="descricao" placeholder="Descrição" required>
    <input type="number" id="valor" placeholder="Valor (R$)" required>
    <select id="tipo" required>
      <option value="">Tipo</option>
      <option value="entrada">Entrada</option>
      <option value="saida">Saída</option>
    </select>
    <input type="date" id="data" required>
    <select id="formaPagamento" required>
      <option value="">Forma de Pagamento</option>
      <option value="PIX">PIX</option>
      <option value="Cartão">Cartão</option>
      <option value="Dinheiro">Dinheiro</option>
      <option value="Cheque">Cheque</option>
    </select>
    <button type="submit">Adicionar Transação</button>
  </form>

  <div id="saldo">Saldo atual: R$ 0,00</div>

  <table>
    <thead>
      <tr>
        <th>Descrição</th>
        <th>Tipo</th>
        <th>Valor (R$)</th>
        <th>Data</th>
        <th>Pagamento</th>
        <th>Ações</th>
      </tr>
    </thead>
    <tbody id="lista-transacoes"></tbody>
  </table>

  <script>
    const form = document.getElementById('form-transacao');
    const lista = document.getElementById('lista-transacoes');
    const saldoDisplay = document.getElementById('saldo');

    let transacoes = JSON.parse(localStorage.getItem('transacoes')) || [];

    function salvarTransacoes() {
      localStorage.setItem('transacoes', JSON.stringify(transacoes));
    }

    function atualizarSaldo() {
      const saldo = transacoes.reduce((total, t) => {
        const valor = parseFloat(t.valor);
        return t.tipo === 'entrada' ? total + valor : total - valor;
      }, 0);
      saldoDisplay.textContent = `Saldo atual: R$ ${saldo.toFixed(2)}`;
    }

    function excluirTransacao(index) {
      transacoes.splice(index, 1);
      salvarTransacoes();
      renderizarTransacoes();
    }

    function renderizarTransacoes() {
      lista.innerHTML = '';
      transacoes.forEach((transacao, index) => {
        const linha = document.createElement('tr');
        linha.innerHTML = `
          <td>${transacao.descricao}</td>
          <td>${transacao.tipo}</td>
          <td>${parseFloat(transacao.valor).toFixed(2)}</td>
          <td>${transacao.data}</td>
          <td>${transacao.forma}</td>
          <td><button class="btn-excluir" onclick="excluirTransacao(${index})">🗑️</button></td>
        `;
        lista.appendChild(linha);
      });
      atualizarSaldo();
    }

    form.addEventListener('submit', function(e) {
      e.preventDefault();

      const descricao = document.getElementById('descricao').value;
      const valor = parseFloat(document.getElementById('valor').value);
      const tipo = document.getElementById('tipo').value;
      const data = document.getElementById('data').value;
      const forma = document.getElementById('formaPagamento').value;

      const novaTransacao = { descricao, valor, tipo, data, forma };
      transacoes.push(novaTransacao);
      salvarTransacoes();
      renderizarTransacoes();
      form.reset();
    });

    renderizarTransacoes();
  </script>
</body>
</html>
