import { useState } from 'react';
import { Card, CardContent } from '@/components/ui/card';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Alert, AlertDescription, AlertTitle } from '@/components/ui/alert'; // Importar Alert para feedback

export default function App() {
  const [step, setStep] = useState('catalog');
  const [selectedPhone, setSelectedPhone] = useState(null);
  const [paymentType, setPaymentType] = useState('');
  const [installmentType, setInstallmentType] = useState('');
  const [form, setForm] = useState({ name: '', phone: '', address: '', cpf: '' });
  const [error, setError] = useState(''); // Estado para mensagens de erro

  const phones = [
    { id: 1, name: 'Poco C75', specs: '8gb - 256gb, Preto', price: 'R$ 2.000' },
    { id: 2, name: 'Redmi Note 12 Pro', specs: '6gb - 128gb, Azul', price: 'R$ 1.800' },
    { id: 3, name: 'iPhone 13', specs: '128gb, Meia-noite', price: 'R$ 4.500' },
  ];

  const handleSendWhatsApp = () => {
    // Validação mais específica
    if (!form.name.trim()) {
      setError('Por favor, digite seu nome.');
      return;
    }
    if (!form.cpf.trim()) {
      setError('Por favor, digite seu CPF.');
      return;
    }
    if (!/^\d{11}$/.test(form.cpf.trim())) { // Validação simples de 11 dígitos para CPF
        setError('CPF inválido. Digite 11 dígitos numéricos.');
        return;
    }
    if (!form.phone.trim()) {
      setError('Por favor, digite seu telefone.');
      return;
    }
    if (!form.address.trim()) {
      setError('Por favor, digite seu endereço.');
      return;
    }
    if (!selectedPhone) {
      setError('Nenhum aparelho selecionado.'); // Caso raro, mas para segurança
      return;
    }
    if (!paymentType) {
      setError('Por favor, selecione uma forma de pagamento.');
      return;
    }
    if (paymentType === 'Parcelado' && !installmentType) {
      setError('Por favor, selecione o tipo de parcelamento.');
      return;
    }

    setError(''); // Limpa qualquer erro anterior

    const message = `*📦 Pedido Diamond Club Imports & Cia*%0A%0A*Nome:* ${form.name}%0A*CPF:* ${form.cpf}%0A*Telefone:* ${form.phone}%0A*Endereço:* ${form.address}%0A*Aparelho:* ${selectedPhone.name} (${selectedPhone.specs}) - ${selectedPhone.price}%0A*Forma de Pagamento:* ${paymentType}${paymentType === 'Parcelado' ? ' - ' + installmentType : ''}%0A${paymentType === 'Parcelado' ? '%0A*Agora, envie a foto do seu documento aqui no WhatsApp para concluir seu pedido.*' : ''}`;

    const url = `https://wa.me/5548999541113?text=${message}`;
    window.open(url, '_blank');
  };

  return (
    <div className="min-h-screen bg-black text-white flex flex-col">
      <header className="bg-black p-4 flex justify-between items-center sticky top-0 z-10">
        <h1 className="text-xl font-bold text-yellow-500">Diamond Club Imports & Cia</h1>
        <div>🛒</div>
      </header>

      <main className="flex-grow p-4">
        {error && ( // Exibe o alerta de erro se houver um
          <Alert variant="destructive" className="mb-4">
            <AlertTitle>Erro no pedido!</AlertTitle>
            <AlertDescription>{error}</AlertDescription>
          </Alert>
        )}

        {step === 'catalog' && (
          <div>
            <h2 className="text-2xl mb-4 text-center">Escolha seu celular</h2>
            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
              {phones.map((phone) => (
                <Card key={phone.id} className="bg-white text-black flex flex-col justify-between">
                  <CardContent className="p-4">
                    <h3 className="font-bold text-lg mb-1">{phone.name}</h3>
                    <p className="text-sm text-gray-700 mb-2">{phone.specs}</p>
                    <p className="text-xl font-semibold text-green-600 mb-4">{phone.price}</p>
                    <Button
                      className="w-full bg-yellow-500 hover:bg-yellow-600 text-black"
                      onClick={() => {
                        setSelectedPhone(phone);
                        setStep('payment');
                        setError(''); // Limpa erro ao avançar
                      }}
                    >
                      Escolher
                    </Button>
                  </CardContent>
                </Card>
              ))}
            </div>
          </div>
        )}

        {step === 'payment' && (
          <div className="text-center">
            <h2 className="text-2xl mb-4">Como você prefere pagar?</h2>
            <div className="flex justify-center space-x-4">
              <Button
                className="bg-yellow-500 hover:bg-yellow-600 text-black"
                onClick={() => {
                  setPaymentType('À Vista');
                  setStep('checkout');
                  setError('');
                }}
              >
                À Vista
              </Button>
              <Button
                className="bg-yellow-500 hover:bg-yellow-600 text-black"
                onClick={() => {
                  setPaymentType('Parcelado');
                  setStep('installment');
                  setError('');
                }}
              >
                Parcelado
              </Button>
            </div>
            <Button
              variant="link"
              className="mt-4 text-gray-400 hover:text-gray-200"
              onClick={() => {
                setStep('catalog');
                setError('');
              }}
            >
              Voltar
            </Button>
          </div>
        )}

        {step === 'installment' && (
          <div className="text-center">
            <h2 className="text-2xl mb-4">Escolha o tipo de parcelamento</h2>
            <div className="flex justify-center space-x-4">
              <Button
                className="bg-yellow-500 hover:bg-yellow-600 text-black"
                onClick={() => {
                  setInstallmentType('Diário');
                  setStep('checkout');
                  setError('');
                }}
              >
                Diário
              </Button>
              <Button
                className="bg-yellow-500 hover:bg-yellow-600 text-black"
                onClick={() => {
                  setInstallmentType('Semanal');
                  setStep('checkout');
                  setError('');
                }}
              >
                Semanal
              </Button>
            </div>
            <Button
              variant="link"
              className="mt-4 text-gray-400 hover:text-gray-200"
              onClick={() => {
                setStep('payment');
                setError('');
              }}
            >
              Voltar
            </Button>
          </div>
        )}

        {step === 'checkout' && (
          <div className="max-w-md mx-auto">
            <h2 className="text-2xl mb-4 text-center">Finalize seu pedido</h2>
            <div className="bg-gray-800 p-6 rounded-lg space-y-4">
              {selectedPhone && (
                <div className="text-center mb-4 p-2 bg-gray-700 rounded-md">
                  <p className="text-lg font-semibold">Aparelho Selecionado:</p>
                  <p>{selectedPhone.name} ({selectedPhone.specs}) - {selectedPhone.price}</p>
                  <p>Pagamento: {paymentType}{paymentType === 'Parcelado' ? ` - ${installmentType}` : ''}</p>
                </div>
              )}
              <div>
                <Label htmlFor="name" className="text-white">Nome Completo</Label>
                <Input
                  id="name"
                  className="text-black bg-white mt-1"
                  value={form.name}
                  onChange={(e) => {setForm({...form, name: e.target.value}); setError('');}}
                />
              </div>
              <div>
                <Label htmlFor="cpf" className="text-white">CPF</Label>
                <Input
                  id="cpf"
                  className="text-black bg-white mt-1"
                  value={form.cpf}
                  onChange={(e) => {setForm({...form, cpf: e.target.value}); setError('');}}
                  maxLength={11} // Limita a 11 dígitos
                  inputMode="numeric" // Sugere teclado numérico
                  pattern="\d*" // Garante que apenas números sejam digitados
                />
              </div>
              <div>
                <Label htmlFor="phone" className="text-white">Telefone (com DDD)</Label>
                <Input
                  id="phone"
                  className="text-black bg-white mt-1"
                  value={form.phone}
                  onChange={(e) => {setForm({...form, phone: e.target.value}); setError('');}}
                  inputMode="tel" // Sugere teclado de telefone
                />
              </div>
              <div>
                <Label htmlFor="address" className="text-white">Endereço Completo</Label>
                <Input
                  id="address"
                  className="text-black bg-white mt-1"
                  value={form.address}
                  onChange={(e) => {setForm({...form, address: e.target.value}); setError('');}}
                />
              </div>
              <Button className="w-full bg-green-500 hover:bg-green-600 text-white font-bold" onClick={handleSendWhatsApp}>
                Finalizar Pedido no WhatsApp
              </Button>
              <Button
                variant="link"
                className="w-full mt-2 text-gray-400 hover:text-gray-200"
                onClick={() => {
                  setStep(paymentType === 'Parcelado' ? 'installment' : 'payment');
                  setError('');
                }}
              >
                Voltar
              </Button>
            </div>
          </div>
        )}
      </main>

      <footer className="bg-black p-4 text-center text-yellow-500 mt-auto">
        Diamond Club Imports & Cia © 2025
      </footer>
    </div>
  );
}# celular-top
celular loja
