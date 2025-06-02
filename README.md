import { useState } from 'react';
import { Card, CardContent } from '@/components/ui/card';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';

export default function App() {
  const [step, setStep] = useState('catalog');
  const [selectedPhone, setSelectedPhone] = useState(null);
  const [paymentType, setPaymentType] = useState('');
  const [installmentType, setInstallmentType] = useState('');
  const [form, setForm] = useState({ name: '', phone: '', address: '', cpf: '' });

  const phones = [
    { id: 1, name: 'Poco C75', specs: '8gb - 256gb, Preto', price: 'R$ 2.000' },
  ];

  const handleSendWhatsApp = () => {
    if (!form.name || !form.cpf || !form.phone || !form.address || !selectedPhone || !paymentType || (paymentType === 'Parcelado' && !installmentType)) {
      alert('Por favor, preencha todos os dados antes de finalizar.');
      return;
    }

    const message = `*📦 Pedido Diamond Club Imports & Cia*%0A%0A*Nome:* ${form.name}%0A*CPF:* ${form.cpf}%0A*Telefone:* ${form.phone}%0A*Endereço:* ${form.address}%0A*Aparelho:* ${selectedPhone.name} (${selectedPhone.specs}) - ${selectedPhone.price}%0A*Forma de Pagamento:* ${paymentType}${paymentType === 'Parcelado' ? ' - ' + installmentType : ''}%0A${paymentType === 'Parcelado' ? '%0A*Agora, envie a foto do seu documento aqui no WhatsApp para concluir seu pedido.*' : ''}`;

    const url = `https://wa.me/5548999541113?text=${message}`;
    window.open(url, '_blank');
  };

  return (
    <div className="min-h-screen bg-black text-white">
      <header className="bg-black p-4 flex justify-between items-center">
        <h1 className="text-xl font-bold text-yellow-500">Diamond Club Imports & Cia</h1>
        <div>🛒</div>
      </header>

      {step === 'catalog' && (
        <div className="p-4">
          <h2 className="text-2xl mb-4">Escolha seu celular</h2>
          <div className="grid grid-cols-2 gap-4">
            {phones.map((phone) => (
              <Card key={phone.id} className="bg-white text-black">
                <CardContent className="p-4">
                  <h3 className="font-bold">{phone.name}</h3>
                  <p>{phone.specs}</p>
                  <p>{phone.price}</p>
                  <Button className="mt-2" onClick={() => {setSelectedPhone(phone); setStep('payment');}}>Escolher</Button>
                </CardContent>
              </Card>
            ))}
          </div>
        </div>
      )}

      {step === 'payment' && (
        <div className="p-4">
          <h2 className="text-2xl mb-4">Forma de pagamento</h2>
          <Button className="mr-2" onClick={() => {setPaymentType('À Vista'); setStep('checkout');}}>À Vista</Button>
          <Button onClick={() => {setPaymentType('Parcelado'); setStep('installment');}}>Parcelado</Button>
        </div>
      )}

      {step === 'installment' && (
        <div className="p-4">
          <h2 className="text-2xl mb-4">Escolha o parcelamento</h2>
          <Button className="mr-2" onClick={() => {setInstallmentType('Diário'); setStep('checkout');}}>Diário</Button>
          <Button onClick={() => {setInstallmentType('Semanal'); setStep('checkout');}}>Semanal</Button>
        </div>
      )}

      {step === 'checkout' && (
        <div className="p-4">
          <h2 className="text-2xl mb-4">Finalizar pedido</h2>
          <div className="space-y-4">
            <div>
              <Label>Nome</Label>
              <Input className="text-black" value={form.name} onChange={(e) => setForm({...form, name: e.target.value})} />
            </div>
            <div>
              <Label>CPF</Label>
              <Input className="text-black" value={form.cpf} onChange={(e) => setForm({...form, cpf: e.target.value})} />
            </div>
            <div>
              <Label>Telefone</Label>
              <Input className="text-black" value={form.phone} onChange={(e) => setForm({...form, phone: e.target.value})} />
            </div>
            <div>
              <Label>Endereço</Label>
              <Input className="text-black" value={form.address} onChange={(e) => setForm({...form, address: e.target.value})} />
            </div>
            <Button className="w-full" onClick={handleSendWhatsApp}>Finalizar no WhatsApp</Button>
          </div>
        </div>
      )}

      <footer className="bg-black p-4 text-center text-yellow-500">
        Diamond Club Imports & Cia
      </footer>
    </div>
  );
}
