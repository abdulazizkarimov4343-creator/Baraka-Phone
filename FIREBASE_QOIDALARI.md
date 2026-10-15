# Mijoz sahifasi uchun Firebase qoidalari

`mijoz.html` sahifasiga mijozlar **login va parolsiz** kiradi. Sahifa ishlashi uchun
Firebase'dagi ruxsat qoidalariga bir nechta qator qo'shish kerak. Busiz mijoz sahifasi
"Sahifani yuklab bo'lmadi" deb chiqadi, arizalar bo'limi esa bo'sh turadi.

## Mijozga nima ochiladi

| Ma'lumot | Mijoz | Xodim (login bilan) |
|---|---|---|
| `settings/main` — narx sozlamalari, dollar kursi | o'qiydi | o'zgarishsiz |
| `settings/catalog` — sotuvdagi telefonlar (model, xotira, rang, sotuv narxi) | o'qiydi | o'zgarishsiz |
| `models` — modellar va bozor narxlari | o'qiydi | o'zgarishsiz |
| `arizalar` — mijoz arizalari | faqat **yangi ariza yuboradi**, o'qiy olmaydi | o'qiydi, holatini o'zgartiradi |
| Shartnomalar, ombor (tannarx, IMEI), xodimlar, rasmlar | **yopiq** | o'zgarishsiz |

`settings/catalog`da tannarx va IMEI yo'q. Bu ro'yxatni admin panel o'zi yangilab turadi.

## Qanday qo'shiladi

1. [Firebase konsoli](https://console.firebase.google.com/project/baraka-32b53/firestore/rules) → **Firestore Database** → **Rules** bo'limini oching.
2. `match /databases/{database}/documents {` qatoridan keyin quyidagi blokni qo'shing.
   Mavjud qoidalarni **o'chirmang**.
3. **Publish** tugmasini bosing.

```
    // ===== Mijoz sahifasi (mijoz.html) — login talab qilinmaydi =====
    match /settings/{id} {
      allow read: if id in ['main', 'catalog'];
    }
    match /models/{id} {
      allow read: if true;
    }
    match /arizalar/{id} {
      // Mijoz faqat yangi ariza yuboradi; maydonlar va hajmi tekshiriladi
      allow create: if request.resource.data.keys().hasOnly(['status', 'no', 'branchId', 'client', 'note', 'device',
                         'deviceUzs', 'stockId', 'ourModel', 'ourUzs', 'diffUzs', 'payType', 'months',
                         'monthlyUzs', 'totalUzs', 'kurs', 'createdAt'])
                    && request.resource.data.status == 'yangi'
                    && request.resource.data.createdAt == request.time
                    && request.resource.data.branchId is string
                    && request.resource.data.client.keys().hasOnly(['name', 'phone'])
                    && request.resource.data.client.name is string
                    && request.resource.data.client.name.size() >= 2
                    && request.resource.data.client.name.size() <= 60
                    && request.resource.data.client.phone is string
                    && request.resource.data.client.phone.matches('^[0-9]{9,15}$')
                    && request.resource.data.note is string
                    && request.resource.data.note.size() <= 500;
      // Arizalarni faqat faol xodimlar ko'radi va boshqaradi
      allow read, update, delete: if request.auth != null
                    && get(/databases/$(database)/documents/profiles/$(request.auth.uid)).data.active == true;
    }
```

## Tekshirish

1. Brauzerning yashirin (inkognito) oynasida `mijoz.html` ni oching. Brendlar ro'yxati chiqishi kerak.
2. Telefonni baholab, sinov uchun ariza yuboring.
3. Admin panel → **Mijoz arizalari** bo'limida ariza ko'rinadi. Sotuvchi ilovasida esa ariza **Arizalar** bo'limida chiqadi (mijoz tanlagan filial sotuvchilariga).

## Eslatma

- Sotuvdagi telefonlar ro'yxati (`settings/catalog`) admin panel ochilganda yangilanadi. Sotuvchi telefonni sotgandan keyin ham u admin panel qayta ochilguncha mijozga ko'rinib turishi mumkin. Ariza baribir sotuvchi orqali tasdiqlanadi.
- Ariza yuborish ochiq bo'lgani uchun soxta arizalar kelishi mumkin. Ko'payib ketsa, Firebase **App Check** yoqiladi.
