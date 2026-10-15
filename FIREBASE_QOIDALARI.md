# Baraka Trade-in — bazani himoyalash

Bazaning asosiy himoyasi bu **Firestore qoidalari**. To'liq qoidalar `firestore.rules` faylida.
Ular Google'ning rasmiy emulyatorida 103 ta sinovdan o'tgan: oddiy ish jarayonlari ham, buzishga urinishlar ham tekshirilgan.

## 1-qadam. Qoidalarni joylash (majburiy, 3 daqiqa)

1. [Firebase konsoli → Firestore → Rules](https://console.firebase.google.com/project/baraka-32b53/firestore/rules) ni oching.
2. Eski matnni **to'liq o'chiring** (Ctrl+A → Delete).
3. GitHub'dagi `firestore.rules` faylini oching, hammasini nusxalab, shu yerga joylang.
4. **Publish** tugmasini bosing.
5. Tekshiring: admin panel, sotuvchi ilovasi va mijoz sahifasi odatdagidek ochilishi kerak.

Agar biror joy "Ruxsat yo'q" deb qolsa, Rules sahifasidagi **History** orqali eski qoidalarga bir bosishda qaytish mumkin.

### Qoidalar nimani taqiqlaydi

| Kim | Nima qila oladi | Nima qila olmaydi |
|---|---|---|
| Mehmon (login yo'q) | Narxlar, modellar, vitrina ko'rish, ariza yuborish | Xodimlar, ombor, shartnoma, pasport, rasm, boshqalarning arizalarini ko'rish; narxni o'zgartirish; soxta yoki zararli maydonli ariza |
| Ro'yxatdan o'tgan begona | Hech narsa | O'zini admin yoki xodim qilish |
| Faol emas (bo'shatilgan) xodim | Hech narsa | O'zini qayta faollashtirish |
| Sotuvchi | O'z filiali ombori, shartnomalari, sotuvlari, arizalari | Boshqa filialni ko'rish; o'zini admin qilish; narx yoki tannarxni o'zgartirish; telefon o'chirish; shartnomani tahrirlash |
| Admin | Hammasi | Tizim egasini o'chirish yoki bloklash; shartnomani o'chirish |
| Tizim egasi (birinchi admin) | Hammasi | — |

Qo'shimcha himoya:
- Ariza maydonlari va ularning hajmi tekshiriladi, shuning uchun admin panelga zararli kod (XSS) kiritib bo'lmaydi.
- Ariza vaqtini soxtalashtirib bo'lmaydi.

## 2-qadam. API kalitini faqat saytga bog'lash (tavsiya, 3 daqiqa)

1. [Google Cloud → Credentials](https://console.cloud.google.com/apis/credentials?project=baraka-32b53) ni oching. **Browser key** (yoki "API key 1") ni bosing.
2. **Application restrictions** → **Websites** ni tanlang va qo'shing:
   - `https://abdulazizkarimov4343-creator.github.io/*`
   - `https://baraka-32b53.firebaseapp.com/*`
3. **Save** tugmasini bosing.

## 3-qadam. Parollar

- **Admin paroli:** kamida 12 belgi. Unda harf, raqam va belgi bo'lsin, boshqa saytlarda ishlatilmagan bo'lsin.
- **Ishdan ketgan xodim:** darhol **Xodimlar → Faol emas** qiling. Shu zahoti kirishi yopiladi.
- **Parolni bilib olishdi deb gumon qilsangiz:** Xodimlar bo'limida parolni almashtiring.

## 4-qadam (ixtiyoriy). Soxta arizalardan himoya — App Check

Ariza yuborish hammaga ochiq, shuning uchun bot ko'plab soxta ariza tashlashi mumkin. Bu ma'lumot o'g'irlash emas, lekin halaqit beradi.
Bunday holat bo'lsa, App Check (reCAPTCHA) yoqiladi. Buning uchun reCAPTCHA kaliti kerak. Shunday vaziyat yuz bersa, ayting, birga ulaymiz.

## 5-qadam (ixtiyoriy). Zaxira nusxa

Firestore'da kunlik avtomatik backup bor, lekin u faqat **Blaze** (pullik, oyiga bir necha sent) tarifida ishlaydi:
Firestore → **Disaster recovery** → **Backups** → kunlik jadval.
