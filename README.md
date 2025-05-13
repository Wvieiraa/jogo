import React, { useState, useEffect } from 'react';

export default function App() {
  const [balanceCrypito, setBalanceCrypito] = useState(0);
  const [clickValue, setClickValue] = useState(1);
  const [autoMiners, setAutoMiners] = useState(0);
  const [level, setLevel] = useState(1);
  const [isMining, setIsMining] = useState(false);
  const [showWithdrawModal, setShowWithdrawModal] = useState(false);
  const [withdrawAmount, setWithdrawAmount] = useState('');
  const [withdrawMethod, setWithdrawMethod] = useState('pix');
  const [usdtBalance, setUsdtBalance] = useState(0);

  // Conversão aproximada: 1 Crypito = 0.0001 USDT
  const crypitoToUSDT = (crypito) => parseFloat((crypito * 0.0001).toFixed(4));
  const usdtToCrypito = (usdt) => parseFloat((usdt / 0.0001).toFixed(2));

  // Função de clique para minerar
  const handleClick = () => {
    setBalanceCrypito((prev) => prev + clickValue);
    setIsMining(true);
    setTimeout(() => setIsMining(false), 300);
  };

  // Efeito para mineração automática
  useEffect(() => {
    if (autoMiners > 0) {
      const interval = setInterval(() => {
        setBalanceCrypito((prev) => prev + autoMiners * clickValue);
      }, 2000);
      return () => clearInterval(interval);
    }
  }, [autoMiners, clickValue]);

  // Upgrade do nível
  const upgradeLevel = () => {
    const cost = level * 50;
    if (balanceCrypito >= cost) {
      setBalanceCrypito((prev) => prev - cost);
      setLevel((prev) => prev + 1);
      setClickValue((prev) => prev + 1);
    }
  };

  // Comprar minerador automático
  const buyAutoMiner = () => {
    const cost = 100 + autoMiners * 50;
    if (balanceCrypito >= cost) {
      setBalanceCrypito((prev) => prev - cost);
      setAutoMiners((prev) => prev + 1);
    }
  };

  // Abrir modal de saque
  const openWithdrawModal = () => {
    setShowWithdrawModal(true);
  };

  // Fechar modal
  const closeWithdrawModal = () => {
    setShowWithdrawModal(false);
    setWithdrawAmount('');
    setWithdrawMethod('pix');
  };

  // Converter para USDT
  const convertToUSDT = () => {
    if (balanceCrypito > 0) {
      const amountInUSDT = crypitoToUSDT(balanceCrypito);
      setUsdtBalance((prev) => prev + amountInUSDT);
      setBalanceCrypito(0);
    }
  };

  // Confirmar saque
  const confirmWithdraw = () => {
    const amount = parseFloat(withdrawAmount);
    if (!isNaN(amount) && amount > 0 && crypitoToUSDT(balanceCrypito) >= amount) {
      setUsdtBalance((prev) => prev + amount);
      setBalanceCrypito((prev) => prev - usdtToCrypito(amount));
      closeWithdrawModal();
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-900 to-black text-white flex flex-col items-center justify-center p-4">
      <h1 className="text-4xl font-bold mb-6 text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-pink-600">
        Crypto Miner Game
      </h1>

      {/* Painel de Moedas */}
      <div className="bg-gray-800 p-6 rounded-xl shadow-lg w-full max-w-md mb-6 text-center">
        <p className="text-xl">Saldo: <span className="font-bold text-green-400">{balanceCrypito} Crypito</span></p>
        <p className="mt-2">≈ <span className="text-yellow-400">{crypitoToUSDT(balanceCrypito)} USDT</span></p>
        <p className="mt-2">Nível: <span className="font-bold text-blue-400">{level}</span> | Mineração por clique: <span className="font-bold text-yellow-400">{clickValue}</span></p>
        <p className="mt-2">Mineradores automáticos: <span className="font-bold text-indigo-400">{autoMiners}</span></p>
      </div>

      {/* Botão de Mineração */}
      <button
        onClick={handleClick}
        className={`w-32 h-32 rounded-full bg-gradient-to-r from-purple-500 to-pink-500 flex items-center justify-center text-white font-bold text-2xl shadow-lg transform transition-transform duration-150 ${
          isMining ? 'scale-95' : 'hover:scale-105'
        }`}
      >
        Minar
      </button>

      <div className="mt-10 grid grid-cols-1 md:grid-cols-3 gap-6 w-full max-w-4xl">
        {/* Upgrade Nível */}
        <div className="bg-gray-800 p-5 rounded-xl shadow-md">
          <h2 className="text-xl font-semibold mb-2">Upgrade de Nível</h2>
          <p>Custo: {level * 50} Crypito</p>
          <button
            onClick={upgradeLevel}
            disabled={balanceCrypito < level * 50}
            className={`mt-3 w-full py-2 px-4 rounded-md ${
              balanceCrypito >= level * 50
                ? 'bg-blue-600 hover:bg-blue-700'
                : 'bg-gray-600 cursor-not-allowed'
            }`}
          >
            Melhorar Nível {level + 1}
          </button>
        </div>

        {/* Comprar Minerador Automático */}
        <div className="bg-gray-800 p-5 rounded-xl shadow-md">
          <h2 className="text-xl font-semibold mb-2">Minerador Automático</h2>
          <p>Custo: {100 + autoMiners * 50} Crypito</p>
          <button
            onClick={buyAutoMiner}
            disabled={balanceCrypito < 100 + autoMiners * 50}
            className={`mt-3 w-full py-2 px-4 rounded-md ${
              balanceCrypito >= 100 + autoMiners * 50
                ? 'bg-green-600 hover:bg-green-700'
                : 'bg-gray-600 cursor-not-allowed'
            }`}
          >
            Comprar Minerador
          </button>
        </div>

        {/* Converter e Sacar */}
        <div className="bg-gray-800 p-5 rounded-xl shadow-md">
          <h2 className="text-xl font-semibold mb-2">Converter/Sacar</h2>
          <p className="mb-2">Seu saldo em USDT: <span className="text-yellow-400">{usdtBalance.toFixed(4)}</span></p>
          <button
            onClick={convertToUSDT}
            disabled={balanceCrypito <= 0}
            className="mt-2 w-full py-2 px-4 rounded-md bg-purple-600 hover:bg-purple-700"
          >
            Converter para USDT
          </button>
          <button
            onClick={openWithdrawModal}
            disabled={usdtBalance <= 0}
            className="mt-2 w-full py-2 px-4 rounded-md bg-red-600 hover:bg-red-700"
          >
            Sacar
          </button>
        </div>
      </div>

      {/* Modal de Saque */}
      {showWithdrawModal && (
        <div className="fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center z-50">
          <div className="bg-gray-800 p-6 rounded-xl shadow-lg w-80">
            <h2 className="text-xl font-bold mb-4">Solicitar Saque</h2>
            <label className="block mb-2">Valor em USDT:</label>
            <input
              type="number"
              value={withdrawAmount}
              onChange={(e) => setWithdrawAmount(e.target.value)}
              placeholder="Digite o valor"
              className="w-full p-2 rounded bg-gray-700 mb-4"
              min="0"
              step="0.01"
            />
            <label className="block mb-2">Método de Saída:</label>
            <select
              value={withdrawMethod}
              onChange={(e) => setWithdrawMethod(e.target.value)}
              className="w-full p-2 rounded bg-gray-700 mb-4"
            >
              <option value="pix">PIX</option>
              <option value="transferencia">Transferência Bancária</option>
            </select>
            <div className="flex justify-end space-x-2">
              <button
                onClick={closeWithdrawModal}
                className="px-4 py-2 bg-gray-600 rounded hover:bg-gray-500"
              >
                Cancelar
              </button>
              <button
                onClick={confirmWithdraw}
                disabled={!withdrawAmount || parseFloat(withdrawAmount) <= 0 || crypitoToUSDT(balanceCrypito) < parseFloat(withdrawAmount)}
                className={`px-4 py-2 rounded ${
                  parseFloat(withdrawAmount) > 0 && crypitoToUSDT(balanceCrypito) >= parseFloat(withdrawAmount)
                    ? 'bg-green-600 hover:bg-green-700'
                    : 'bg-gray-600 cursor-not-allowed'
                }`}
              >
                Confirmar
              </button>
            </div>
          </div>
        </div>
      )}

      {/* Mensagem de instrução */}
      <div className="mt-8 text-sm text-gray-400 text-center">
        <p>Clique no botão "Minar" para ganhar Crypito ou compre mineradores automáticos para ganhar passivamente.</p>
        <p className="mt-2">Use upgrades para aumentar sua eficiência de mineração!</p>
        <p className="mt-2">Converta seu Crypito para USDT e realize saques via PIX ou transferência bancária.</p>
      </div>
    </div>

