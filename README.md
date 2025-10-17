import React, { useState } from "react";
import { ShoppingCart, CreditCard, Check, X } from "lucide-react";

// BODA STORE - Single-file React component (Tailwind + shadcn/ui friendly)
// Default export a component that renders a simple e-store for selling PUBG UC (شدات)
// - RTL layout (Arabic)
// - Product grid, cart sidebar, checkout modal (mock payment)
// - No backend: this is a front-end prototype ready to wire to APIs

export default function BodaStore() {
  const products = [
    { id: 1, title: "حزمة 60 UC", price: 0.99, uc: 60, sku: "UC60" },
    { id: 2, title: "حزمة 300 UC", price: 4.99, uc: 300, sku: "UC300" },
    { id: 3, title: "حزمة 600 UC", price: 9.99, uc: 600, sku: "UC600" },
    { id: 4, title: "حزمة 1250 UC", price: 19.99, uc: 1250, sku: "UC1250" },
    { id: 5, title: "حزمة 3000 UC", price: 44.99, uc: 3000, sku: "UC3000" }
  ];

  const [cart, setCart] = useState([]);
  const [showCart, setShowCart] = useState(false);
  const [showCheckout, setShowCheckout] = useState(false);
  const [playerId, setPlayerId] = useState("");
  const [paymentMethod, setPaymentMethod] = useState("vodafone_cash");
  const [phone, setPhone] = useState("");
  const [successMessage, setSuccessMessage] = useState("");

  function addToCart(p) {
    setCart((c) => {
      const found = c.find((i) => i.id === p.id);
      if (found) return c.map((i) => (i.id === p.id ? { ...i, qty: i.qty + 1 } : i));
      return [...c, { ...p, qty: 1 }];
    });
    setShowCart(true);
  }

  function updateQty(id, qty) {
    setCart((c) => c.map((i) => (i.id === id ? { ...i, qty: Math.max(1, qty) } : i)));
  }

  function removeFromCart(id) {
    setCart((c) => c.filter((i) => i.id !== id));
  }

  function cartTotal() {
    return cart.reduce((s, i) => s + i.price * i.qty, 0);
  }

  function placeOrder() {
    // basic validation
    if (!playerId.trim()) return alert("أدخل Player ID الخاص بك لكي نرسل الشدات");
    if (cart.length === 0) return alert("عربتك فارغة");

    // Mock payment flow: in production, call your payment API here
    const order = {
      playerId,
      phone,
      paymentMethod,
      items: cart,
      total: cartTotal()
    };

    // Simulate success
    setSuccessMessage(`تم إنشاء الطلب بنجاح!\nمعرّف اللاعب: ${order.playerId}\nالمبلغ: $${order.total.toFixed(2)}\nطريقة الدفع: ${order.paymentMethod}`);
    setCart([]);
    setShowCheckout(false);
  }

  return (
    <div className="min-h-screen bg-slate-50 p-4" dir="rtl">
      <header className="max-w-6xl mx-auto flex items-center justify-between py-4">
        <div>
          <h1 className="text-3xl font-extrabold">BODA STORE</h1>
          <p className="text-sm text-slate-600">متجر موثوق لبيع شدات PUBG Mobile — آمن وسهل</p>
        </div>
        <div className="flex items-center gap-4">
          <button
            onClick={() => setShowCart((s) => !s)}
            className="flex items-center gap-2 bg-white shadow px-4 py-2 rounded-lg"
            aria-label="عرض السلة"
          >
            <ShoppingCart className="h-5 w-5" />
            <span>السلة ({cart.reduce((a, b) => a + b.qty, 0)})</span>
          </button>
          <button
            onClick={() => { setShowCheckout(true); setShowCart(false); }}
            className="flex items-center gap-2 bg-amber-500 text-white px-4 py-2 rounded-lg shadow"
          >
            <CreditCard className="h-5 w-5" />
            <span>الذهاب للدفع</span>
          </button>
        </div>
      </header>

      <main className="max-w-6xl mx-auto grid grid-cols-1 lg:grid-cols-4 gap-6">
        <section className="lg:col-span-3">
          <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-6">
            {products.map((p) => (
              <article key={p.id} className="bg-white rounded-2xl p-4 shadow">
                <div className="h-36 bg-slate-100 rounded-lg flex items-center justify-center mb-3">
                  <span className="text-xl font-bold">{p.uc} UC</span>
                </div>
                <h3 className="font-semibold">{p.title}</h3>
                <p className="text-sm text-slate-500">رمز المنتج: {p.sku}</p>
                <div className="mt-3 flex items-center justify-between">
                  <div>
                    <div className="text-lg font-bold">${p.price.toFixed(2)}</div>
                    <div className="text-xs text-slate-500">السعر تقريبي وقد يختلف حسب طريقة الدفع</div>
                  </div>
                  <div className="flex flex-col gap-2">
                    <button onClick={() => addToCart(p)} className="px-3 py-2 rounded-lg bg-emerald-500 text-white">أضف إلى السلة</button>
                    <button onClick={() => { setCart([{ ...p, qty: 1 }]); setShowCheckout(true); }} className="px-3 py-2 rounded-lg border">شراء الآن</button>
                  </div>
                </div>
              </article>
            ))}
          </div>
        </section>

        <aside className="lg:col-span-1">
          <div className="sticky top-6 bg-white p-4 rounded-2xl shadow">
            <h4 className="font-bold">مُلخّص الطلب</h4>
            <div className="mt-3 space-y-3">
              {cart.length === 0 ? (
                <div className="text-sm text-slate-500">السلة فارغة — أضف منتجات للبدء</div>
              ) : (
                cart.map((it) => (
                  <div key={it.id} className="flex items-center justify-between">
                    <div>
                      <div className="font-semibold">{it.title}</div>
                      <div className="text-xs text-slate-500">{it.uc} UC × {it.qty}</div>
                    </div>
                    <div className="text-right">
                      <div>${(it.price * it.qty).toFixed(2)}</div>
                      <div className="flex items-center gap-2 mt-2">
                        <input type="number" value={it.qty} min={1} onChange={(e) => updateQty(it.id, Number(e.target.value))} className="w-16 border rounded px-2 py-1 text-sm" />
                        <button onClick={() => removeFromCart(it.id)} className="p-1 rounded bg-red-50 text-red-600"><X className="h-4 w-4" /></button>
                      </div>
                    </div>
                  </div>
                ))
              )}
            </div>

            <div className="mt-4 border-t pt-4">
              <div className="flex justify-between">
                <div className="text-sm text-slate-600">المجموع</div>
                <div className="font-semibold">${cartTotal().toFixed(2)}</div>
              </div>
              <div className="mt-3 flex gap-2">
                <button onClick={() => { setShowCheckout(true); }} className="flex-1 bg-amber-500 text-white py-2 rounded-lg">الدفع الآن</button>
                <button onClick={() => setCart([])} className="flex-1 border py-2 rounded-lg">تفريغ السلة</button>
              </div>
            </div>
          </div>

          <div className="mt-4 text-xs text-slate-500">
            <p>ملاحظة: هذا متجر تجريبي. لعمليات فعلية ربط بوابة دفع موثوقة (مثل M-Pesa, Vodafone Cash, أو بوابات بنكية) وتفعيل KYC للبائع.</p>
          </div>
        </aside>
      </main>

      {/* Checkout Modal */}
      {showCheckout && (
        <div className="fixed inset-0 bg-black/40 flex items-center justify-center p-4">
          <div className="bg-white max-w-lg w-full rounded-2xl p-6">
            <div className="flex justify-between items-center mb-4">
              <h3 className="text-xl font-bold">نموذج الدفع</h3>
              <button onClick={() => setShowCheckout(false)} className="text-slate-500">إلغاء</button>
            </div>

            <div className="space-y-3">
              <div>
                <label className="block text-sm">معرّف اللاعب (Player ID)</label>
                <input value={playerId} onChange={(e) => setPlayerId(e.target.value)} placeholder="ادخل Player ID" className="w-full mt-1 border rounded px-3 py-2" />
              </div>

              <div>
                <label className="block text-sm">رقم الهاتف (اختياري)</label>
                <input value={phone} onChange={(e) => setPhone(e.target.value)} placeholder="مثال: 010xxxxxxxx" className="w-full mt-1 border rounded px-3 py-2" />
              </div>

              <div>
                <label className="block text-sm">طريقة الدفع</label>
                <select value={paymentMethod} onChange={(e) => setPaymentMethod(e.target.value)} className="w-full mt-1 border rounded px-3 py-2">
                  <option value="vodafone_cash">Vodafone Cash (محلي)</option>
                  <option value="visa_virtual">فيزا افتراضية (Online Visa)</option>
                  <option value="mastercard">Mastercard</option>
                  <option value="paypal">PayPal / حلول دولية</option>
                </select>
              </div>

              <div className="pt-3 border-t">
                <div className="flex justify-between items-center">
                  <div>
                    <div className="text-sm text-slate-600">المبلغ المطلوب</div>
                    <div className="text-lg font-bold">${cartTotal().toFixed(2)}</div>
                  </div>
                  <div>
                    <button onClick={placeOrder} className="bg-emerald-500 text-white px-4 py-2 rounded-lg flex items-center gap-2">
                      <Check className="h-4 w-4" />
                      تأكيد و إتمام الدفع
                    </button>
                  </div>
                </div>

                <div className="mt-3 text-xs text-slate-500">تنبيه: في نسخة الإنتاج اربط بوابة دفع حقيقية ووفّر إيصالات تلقائية للعميل.</div>
              </div>
            </div>
          </div>
        </div>
      )}

      {/* Success toast / modal */}
      {successMessage && (
        <div className="fixed bottom-6 left-1/2 -translate-x-1/2 bg-white shadow rounded-lg p-4 max-w-xl w-full">
          <div className="flex justify-between items-start gap-4">
            <div>
              <h4 className="font-bold">تم الطلب!</h4>
              <pre className="text-sm whitespace-pre-wrap mt-2">{successMessage}</pre>
            </div>
            <div>
              <button onClick={() => setSuccessMessage("")} className="bg-red-50 text-red-600 px-3 py-1 rounded">اغلاق</button>
            </div>
          </div>
        </div>
      )}

      <footer className="max-w-6xl mx-auto text-center text-xs text-slate-500 mt-10 mb-6">
        © {new Date().getFullYear()} BODA STORE — جميع الحقوق محفوظة. استخدم طرق دفع قانونية وموثوقة.
      </footer>
    </div>
  );
}
