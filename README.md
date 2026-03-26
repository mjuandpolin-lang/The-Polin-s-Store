# The-Polin-s-Store
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Checkout - ThriftMarket Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        .modal-active { display: flex !important; }
        @keyframes scaleIn { from { transform: scale(0.8); opacity: 0; } to { transform: scale(1); opacity: 1; } }
        .animate-pop { animation: scaleIn 0.3s ease-out forwards; }
    </style>
</head>
<body class="bg-gray-100 font-sans pb-10 text-gray-800">

    <div class="max-w-md mx-auto bg-white min-h-screen shadow-2xl overflow-hidden relative">
        <div class="bg-emerald-600 p-8 text-white rounded-b-[40px] shadow-lg text-center">
            <h1 class="text-3xl font-extrabold tracking-tight">ThriftMarket 👕</h1>
            <p class="text-emerald-100 text-sm mt-1">Kualitas Bintang 5, Harga Kaki 5</p>
        </div>

        <div class="p-6">
            <h2 class="font-bold text-lg mb-3 flex items-center">
                <span class="bg-emerald-100 text-emerald-600 p-1 rounded-md mr-2">🛒</span> Produk
            </h2>
            <div class="flex bg-gray-50 p-4 rounded-2xl border border-gray-100 mb-6">
                <img src="https://via.placeholder.com/80" class="rounded-xl shadow-sm mr-4" alt="Product">
                <div class="flex-1">
                    <h3 class="font-bold text-gray-800 text-base">Vintage Hoodie 90s</h3>
                    <p class="text-emerald-600 font-bold text-lg mt-1">Rp 150.000</p>
                </div>
            </div>

            <div class="space-y-4 mb-8">
                <h2 class="font-bold text-lg flex items-center"><span class="bg-emerald-100 text-emerald-600 p-1 rounded-md mr-2">📍</span> Pengiriman</h2>
                <input type="text" id="nama" placeholder="Nama Lengkap" class="w-full p-4 bg-gray-50 border border-gray-200 rounded-2xl focus:ring-2 focus:ring-emerald-500 outline-none transition">
                <textarea id="alamat" rows="2" placeholder="Alamat Lengkap" class="w-full p-4 bg-gray-50 border border-gray-200 rounded-2xl focus:ring-2 focus:ring-emerald-500 outline-none transition"></textarea>
            </div>

            <div class="grid grid-cols-1 gap-4 mb-8">
                <div>
                    <h2 class="font-bold text-lg mb-3 flex items-center"><span class="bg-emerald-100 text-emerald-600 p-1 rounded-md mr-2">🚚</span> Kurir</h2>
                    <select id="kurir" onchange="handleKurirChange()" class="w-full p-4 bg-gray-50 border border-gray-200 rounded-2xl outline-none focus:ring-2 focus:ring-emerald-500">
                        <option value="0|Pilih">-- Pilih Kurir --</option>
                        <option value="15000|J&T Express">J&T Express (Rp 15.000)</option>
                        <option value="12000|JNE Reguler">JNE Reguler (Rp 12.000)</option>
                    </select>
                </div>
                <div>
                    <h2 class="font-bold text-lg mb-3 flex items-center"><span class="bg-emerald-100 text-emerald-600 p-1 rounded-md mr-2">💳</span> Pembayaran</h2>
                    <select id="metode" class="w-full p-4 bg-gray-50 border border-gray-200 rounded-2xl outline-none focus:ring-2 focus:ring-emerald-500">
                        <option value="">-- Pilih Metode --</option>
                        <option value="Transfer BCA">Transfer BCA</option>
                        <option value="Transfer Mandiri">Transfer Mandiri</option>
                        <option value="DANA">DANA</option>
                        <option value="GoPay">GoPay</option>
                    </select>
                </div>
            </div>

            <div class="p-6 bg-emerald-900 text-white rounded-[35px] shadow-2xl">
                <div class="flex justify-between text-emerald-200 text-sm mb-4">
                    <span>Total Tagihan:</span>
                    <span id="display-total" class="text-2xl font-bold text-white">Rp 150.000</span>
                </div>
                <button onclick="prosesCheckout()" class="w-full bg-emerald-400 text-emerald-950 font-black py-5 rounded-2xl hover:bg-emerald-300 transition-all transform active:scale-95 shadow-lg uppercase tracking-wider">
                    Beli & Bayar Sekarang 🚀
                </button>
            </div>
        </div>

        <div id="thankYouModal" class="fixed inset-0 bg-black/60 z-50 hidden items-center justify-center p-6 backdrop-blur-sm">
            <div class="bg-white rounded-[40px] p-8 w-full max-w-xs text-center animate-pop shadow-2xl">
                <div class="w-20 h-20 bg-emerald-100 text-emerald-600 rounded-full flex items-center justify-center mx-auto mb-4 text-4xl">
                    ✔
                </div>
                <h2 class="text-2xl font-extrabold text-gray-800 mb-2">Terima Kasih!</h2>
                <p class="text-gray-500 text-sm mb-6 leading-relaxed">Pesananmu sudah tercatat. Kamu akan diarahkan ke WhatsApp untuk penyelesaian pembayaran.</p>
                <div class="w-full bg-gray-100 h-2 rounded-full overflow-hidden">
                    <div id="progressBar" class="bg-emerald-500 h-full w-0 transition-all duration-1000"></div>
                </div>
                <p class="text-[10px] text-gray-400 mt-2 uppercase tracking-widest font-bold">Mengarahkan ke WhatsApp...</p>
            </div>
        </div>
    </div>

    <script>
        const HARGA_PRODUK = 150000;
        let ongkirTerpilih = 0;
        let kurirTerpilih = "";

        function handleKurirChange() {
            const select = document.getElementById('kurir');
            const val = select.value.split('|');
            ongkirTerpilih = parseInt(val[0]);
            kurirTerpilih = val[1];
            
            const total = HARGA_PRODUK + ongkirTerpilih;
            document.getElementById('display-total').innerText = "Rp " + total.toLocaleString('id-ID');
        }

        function prosesCheckout() {
            const nama = document.getElementById('nama').value;
            const alamat = document.getElementById('alamat').value;
            const metode = document.getElementById('metode').value;

            if (!nama || !alamat || !metode || ongkirTerpilih === 0) {
                alert("Lengkapi data pengiriman dan pembayaran dulu ya!");
                return;
            }

            // Tampilkan Modal
            const modal = document.getElementById('thankYouModal');
            const progress = document.getElementById('progressBar');
            modal.classList.add('modal-active');
            
            // Animasi Loading Bar (2 detik)
            setTimeout(() => { progress.style.width = '100%'; }, 100);

            // Redirect WA setelah 2.5 detik
            setTimeout(() => {
                const totalTagihan = HARGA_PRODUK + ongkirTerpilih;
                const nomorAdmin = "628123456789"; // GANTI DENGAN NOMOR KAMU
                const pesan = `*PESANAN BARU - THRIFTMARKET*%0A------------------------------------%0A*👤 Nama:* ${nama}%0A*📍 Alamat:* ${alamat}%0A------------------------------------%0A*📦 Produk:* Vintage Hoodie 90s%0A*🚚 Kurir:* ${kurirTerpilih}%0A*💳 Metode:* ${metode}%0A*💰 TOTAL:* Rp ${totalTagihan.toLocaleString('id-ID')}%0A------------------------------------%0AMohon info pembayarannya ya!`;
                
                window.location.href = `https://wa.me/${nomorAdmin}?text=${pesan}`;
            }, 2500);
        }
    </script>
</body>
</html>
