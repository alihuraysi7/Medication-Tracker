# Medication-Tracker
// package.json
{
  "name": "medication-tracker-pwa",
  "version": "1.0.0",
  "description": "تطبيق تنظيم الأدوية - Progressive Web App",
  "main": "index.html",
  "scripts": {
    "start": "npx serve .",
    "build": "echo 'Build complete'",
    "deploy": "gh-pages -d .",
    "dev": "python -m http.server 8000"
  },
  "keywords": [
    "medication",
    "tracker",
    "pwa",
    "arabic",
    "health",
    "medicine"
  ],
  "author": "Your Name",
  "license": "MIT",
  "devDependencies": {
    "gh-pages": "^6.0.0"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/YOUR_USERNAME/medication-tracker-pwa.git"
  },
  "homepage": "https://YOUR_USERNAME.github.io/medication-tracker-pwa"
}

---

<!-- _config.yml للـ GitHub Pages -->
title: تطبيق تنظيم الأدوية
description: تطبيق ويب تقدمي لتتبع مواعيد الأدوية
theme: minima
lang: ar
dir: rtl

---

/* styles.css - ملف الأنماط المخصص */
@import url('https://fonts.googleapis.com/css2?family=Cairo:wght@300;400;600;700&display=swap');

:root {
  --primary-color: #3B82F6;
  --primary-dark: #1E40AF;
  --secondary-color: #10B981;
  --warning-color: #F59E0B;
  --danger-color: #EF4444;
  --gray-50: #F9FAFB;
  --gray-100: #F3F4F6;
  --gray-200: #E5E7EB;
  --gray-300: #D1D5DB;
  --gray-400: #9CA3AF;
  --gray-500: #6B7280;
  --gray-600: #4B5563;
  --gray-700: #374151;
  --gray-800: #1F2937;
  --gray-900: #111827;
}

* {
  box-sizing: border-box;
}

body {
  font-family: 'Cairo', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  margin: 0;
  padding: 0;
  direction: rtl;
  text-align: right;
}

/* PWA Splash Screen Styles */
.splash-screen {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  background: linear-gradient(135deg, var(--primary-color), var(--primary-dark));
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
  transition: opacity 0.5s ease-out;
}

.splash-screen.hidden {
  opacity: 0;
  pointer-events: none;
}

.splash-logo {
  width: 120px;
  height: 120px;
  background: white;
  border-radius: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 20px 40px rgba(0,0,0,0.2);
  animation: bounceIn 1s ease-out;
}

@keyframes bounceIn {
  0% {
    transform: scale(0.3);
    opacity: 0;
  }
  50% {
    transform: scale(1.05);
  }
  70% {
    transform: scale(0.9);
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

.loading-spinner {
  margin-top: 20px;
  width: 40px;
  height: 40px;
  border: 4px solid rgba(255,255,255,0.3);
  border-top: 4px solid white;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

/* Responsive Design */
@media (max-width: 768px) {
  .splash-logo {
    width: 100px;
    height: 100px;
  }
}

/* Custom Scrollbar */
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: var(--gray-100);
}

::-webkit-scrollbar-thumb {
  background: var(--gray-400);
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: var(--gray-500);
}

/* Button Animations */
.btn-animate {
  transition: all 0.2s ease-in-out;
}

.btn-animate:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
}

.btn-animate:active {
  transform: translateY(0);
}

/* Card Hover Effects */
.card-hover {
  transition: all 0.3s ease-in-out;
}

.card-hover:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0,0,0,0.1);
}

/* Fade In Animation */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.fade-in {
  animation: fadeIn 0.5s ease-out;
}

/* Status Indicators */
.status-indicator {
  position: relative;
  overflow: hidden;
}

.status-indicator::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
  transition: left 0.5s;
}

.status-indicator:hover::before {
  left: 100%;
}

---

/* notification.js - نظام الإشعارات */
class NotificationManager {
  constructor() {
    this.permission = 'default';
    this.init();
  }

  async init() {
    if ('Notification' in window) {
      this.permission = await Notification.requestPermission();
    }
  }

  async scheduleReminder(medication, time) {
    if (this.permission !== 'granted') {
      console.log('Notification permission not granted');
      return;
    }

    const now = new Date();
    const [hours, minutes] = time.split(':');
    const reminderTime = new Date();
    reminderTime.setHours(parseInt(hours), parseInt(minutes), 0, 0);

    if (reminderTime <= now) {
      reminderTime.setDate(reminderTime.getDate() + 1);
    }

    const timeToReminder = reminderTime.getTime() - now.getTime();

    setTimeout(() => {
      this.showNotification(medication);
    }, timeToReminder);
  }

  showNotification(medication) {
    if (this.permission === 'granted') {
      const notification = new Notification(`حان وقت تناول ${medication.name}`, {
        body: `الجرعة: ${medication.dosage} - ${medication.frequency}`,
        icon: '/icons/icon-192x192.png',
        badge: '/icons/icon-72x72.png',
        tag: `medication-${medication.id}`,
        requireInteraction: true,
        vibrate: [200, 100, 200],
        actions: [
          {
            action: 'mark-taken',
            title: 'تم التناول'
          },
          {
            action: 'remind-later',
            title: 'تذكير لاحقاً'
          }
        ]
      });

      notification.onclick = () => {
        window.focus();
        notification.close();
      };

      setTimeout(() => {
        notification.close();
      }, 10000);
    }
  }

  async scheduleAllReminders(medications) {
    medications.forEach(med => {
      if (!med.taken) {
        const time = this.getMedicationTime(med);
        this.scheduleReminder(med, time);
      }
    });
  }

  getMedicationTime(med) {
    const now = new Date();
    const isWeekend = now.getDay() === 5 || now.getDay() === 6;
    const isHoliday = localStorage.getItem('isHoliday') === 'true';

    if (isHoliday && med.timeHoliday) return med.timeHoliday;
    if (isWeekend && med.timeWeekend) return med.timeWeekend;
    return med.timeWeekday;
  }
}

// Initialize notification manager
const notificationManager = new NotificationManager();

---

/* utils.js - دوال مساعدة */
class MedicationUtils {
  static formatTime(timeString) {
    if (!timeString) return '';
    const [hours, minutes] = timeString.split(':');
    const date = new Date();
    date.setHours(parseInt(hours), parseInt(minutes));
    return date.toLocaleTimeString('ar-SA', {
      hour: '2-digit',
      minute: '2-digit',
      hour12: true
    });
  }

  static isTimeToTake(medicationTime, reminderMinutes = 5) {
    const now = new Date();
    const [hours, minutes] = medicationTime.split(':');
    const medTime = new Date();
    medTime.setHours(parseInt(hours), parseInt(minutes), 0, 0);
    
    const timeDiff = medTime.getTime() - now.getTime();
    const minutesDiff = timeDiff / (1000 * 60);
    
    return minutesDiff <= reminderMinutes && minutesDiff >= 0;
  }

  static exportData(medications) {
    const data = {
      medications,
      exportDate: new Date().toISOString(),
      version: '1.0.0'
    };
    
    const blob = new Blob([JSON.stringify(data, null, 2)], {
      type: 'application/json'
    });
    
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `medication-backup-${new Date().toISOString().split('T')[0]}.json`;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);
  }

  static async importData(file) {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.onload = (e) => {
        try {
          const data = JSON.parse(e.target.result);
          if (data.medications && Array.isArray(data.medications)) {
            resolve(data.medications);
          } else {
            reject(new Error('Invalid file format'));
          }
        } catch (error) {
          reject(error);
        }
      };
      reader.readAsText(file);
    });
  }

  static generateStats(medications) {
    const taken = medications.filter(med => med.taken).length;
    const remaining = medications.length - taken;
    const percentage = medications.length > 0 ? (taken / medications.length) * 100 : 0;
    
    return {
      total: medications.length,
      taken,
      remaining,
      percentage: Math.round(percentage)
    };
  }

  static getUpcomingMedications(medications, hours = 2) {
    const now = new Date();
    const upcoming = [];
    
    medications.forEach(med => {
      if (!med.taken) {
        const time = this.getMedicationTime(med);
        const [medHours, medMinutes] = time.split(':');
        const medTime = new Date();
        medTime.setHours(parseInt(medHours), parseInt(medMinutes), 0, 0);
        
        if (medTime > now) {
          const timeDiff = medTime.getTime() - now.getTime();
          const hoursDiff = timeDiff / (1000 * 60 * 60);
          
          if (hoursDiff <= hours) {
            upcoming.push({
              ...med,
              timeUntil: this.formatTimeUntil(timeDiff)
            });
          }
        }
      }
    });
    
    return upcoming.sort((a, b) => a.timeUntil.localeCompare(b.timeUntil));
  }

  static formatTimeUntil(milliseconds) {
    const minutes = Math.floor(milliseconds / (1000 * 60));
    const hours = Math.floor(minutes / 60);
    const remainingMinutes = minutes % 60;
    
    if (hours > 0) {
      return `${hours} ساعة و ${remainingMinutes} دقيقة`;
    } else {
      return `${remainingMinutes} دقيقة`;
    }
  }

  static getMedicationTime(med) {
    const now = new Date();
    const isWeekend = now.getDay() === 5 || now.getDay() === 6;
    const isHoliday = localStorage.getItem('isHoliday') === 'true';

    if (isHoliday && med.timeHoliday) return med.timeHoliday;
    if (isWeekend && med.timeWeekend) return med.timeWeekend;
    return med.timeWeekday;
  }
}

---

<!-- netlify.toml - إعدادات Netlify -->
[build]
  publish = "."
  command = "echo 'Static site - no build needed'"

[build.environment]
  NODE_VERSION = "18"

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"

[[headers]]
  for = "/manifest.json"
  [headers.values]
    Content-Type = "application/manifest+json"

[[headers]]
  for = "/sw.js"
  [headers.values]
    Content-Type = "application/javascript"
    Cache-Control = "no-cache"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200

---

<!-- vercel.json - إعدادات Vercel -->
{
  "version": 2,
  "builds": [
    {
      "src": "index.html",
      "use": "@vercel/static"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/index.html"
    }
  ],
  "headers": [
    {
      "source": "/manifest.json",
      "headers": [
        {
          "key": "Content-Type",
          "value": "application/manifest+json"
        }
      ]
    },
    {
      "source": "/sw.js",
      "headers": [
        {
          "key": "Content-Type",
          "value": "application/javascript"
        },
        {
          "key": "Cache-Control",
          "value": "no-cache"
        }
      ]
    }
  ]
}

---

# .gitignore
# Dependencies
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Production builds
/build
/dist

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# IDE files
.vscode/
.idea/
*.swp
*.swo

# OS generated files
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# Logs
logs
*.log

# Cache
.cache/
.parcel-cache/

---

# deploy.sh - سكريبت النشر
#!/bin/bash

echo "🚀 بدء عملية النشر..."

# التحقق من Git
if ! command -v git &> /dev/null; then
    echo "❌ Git غير مثبت"
    exit 1
fi

# التحقق من الملفات المطلوبة
required_files=("index.html" "manifest.json" "sw.js")
for file in "${required_files[@]}"; do
    if [ ! -f "$file" ]; then
        echo "❌ الملف المطلوب غير موجود: $file"
        exit 1
    fi
done

# إنشاء مجلد الأيقونات إذا لم يكن موجوداً
if [ ! -d "icons" ]; then
    mkdir icons
    echo "📁 تم إنشاء مجلد الأيقونات"
fi

# إضافة الملفات لـ Git
git add .

# التحقق من وجود تغييرات
if git diff --staged --quiet; then
    echo "ℹ️ لا توجد تغييرات للنشر"
    exit 0
fi

# الحصول على رسالة الـ commit
read -p "📝 ادخل رسالة الـ commit (أو اضغط Enter للرسالة الافتراضية): " commit_msg
if [ -z "$commit_msg" ]; then
    commit_msg="تحديث التطبيق - $(date '+%Y-%m-%d %H:%M')"
fi

# تنفيذ الـ commit
git commit -m "$commit_msg"

# النشر
echo "🔄 جاري رفع التغييرات..."
git push origin main

echo "✅ تم النشر بنجاح!"
echo "🌐 التطبيق متاح على: https://YOUR_USERNAME.github.io/medication-tracker-pwa"

---

# CONTRIBUTING.md
# المساهمة في تطبيق تنظيم الأدوية

نرحب بمساهماتكم لتحسين هذا التطبيق! 🎉

## كيفية المساهمة

### 🐛 الإبلاغ عن الأخطاء

1. تأكد من أن المشكلة لم يتم الإبلاغ عنها مسبقاً
2. أنشئ Issue جديد
3. اكتب وصفاً واضحاً للمشكلة
4. أضف خطوات إعادة إنتاج المشكلة
5. أضف لقطات شاشة إن أمكن

### ✨ اقتراح ميزات جديدة

1. أنشئ Issue جديد مع تسمية "Feature Request"
2. اشرح الميزة المقترحة وفائدتها
3. أضف أمثلة أو مراجع إن وجدت

### 💻 المساهمة بالكود

1. **Fork المستودع**
2. **أنشئ branch جديد:**
   ```bash
   git checkout -b feature/اسم-الميزة
   ```
3. **اكتب الكود:**
   - اتبع أسلوب الكتابة الموجود
   - أضف تعليقات باللغة العربية
   - تأكد من أن الكود يعمل على الأجهزة المختلفة
4. **اختبر التغييرات:**
   - تأكد من عمل التطبيق بشكل صحيح
   - اختبر على متصفحات مختلفة
5. **أرسل Pull Request:**
   - اكتب وصفاً واضحاً للتغييرات
   - اربط الـ PR بالـ Issue المتعلق

## 📋 معايير الكود

### JavaScript
- استخدم `const` و `let` بدلاً من `var`
- اكتب دوال واضحة ومفهومة
- أضف تعليقات للأجزاء المعقدة

### CSS
- استخدم أسماء classes واضحة
- اتبع منهجية BEM عند الحاجة
- تأكد من الاستجابة للأجهزة المختلفة

### HTML
- استخدم HTML5 semantic elements
- تأكد من إمكانية الوصول (Accessibility)
- اضبط اتجاه النص للعربية

## 🌍 الترجمة

نرحب بالمساهمة في ترجمة التطبيق للغات أخرى:

1. أنشئ ملف ترجمة جديد: `translations/[language-code].json`
2. ترجم كل النصوص
3. اختبر الترجمة
4. أرسل Pull Request

## 📱 اختبار PWA

عند المساهمة، تأكد من اختبار:

- [ ] تثبيت التطبيق على الهاتف
- [ ] العمل بدون إنترنت
- [ ] الإشعارات (إن كانت مفعلة)
- [ ] سرعة التحميل
- [ ] الاستجابة للأجهزة المختلفة

## 📞 التواصل

- أنشئ Issue للنقاش
- استخدم Discussions للأسئلة العامة

شكراً لمساهماتكم! 💙

---

# CHANGELOG.md
# سجل التغييرات

## [1.0.0] - 2024-01-XX

### ✨ ميزات جديدة
- إطلاق التطبيق كـ Progressive Web App
- إمكانية إضافة وتتبع الأدوية
- مواعيد مختلفة للأيام العادية والويكند والأجازات
- حفظ البيانات محلياً في المتصفح
- واجهة مستخدم عربية بالكامل
- إحصائيات يومية للأدوية
- إمكانية تثبيت التطبيق على الهاتف

### 🛠️ التحسينات التقنية
- Service Worker للعمل بدون إنترنت
- Web App Manifest كامل
- تصميم متجاوب للأجهزة المختلفة
- تحسين الأداء والسرعة

### 📱 الدعم
- يعمل على جميع المتصفحات الحديثة
- دعم كامل للهواتف والأجهزة اللوحية
- تثبيت كتطبيق أصلي على iOS و Android

---

# API.md
# وثائق API المحلي

## نظام التخزين المحلي

### حفظ الأدوية
```javascript
localStorage.setItem('medications', JSON.stringify(medications));
```

### استرجاع الأدوية
```javascript
const medications = JSON.parse(localStorage.getItem('medications') || '[]');
```

### حفظ إعدادات الأجازة
```javascript
localStorage.setItem('isHoliday', 'true');
```

## هيكل بيانات الدواء

```javascript
{
  id: timestamp,           // معرف فريد
  name: "string",         // اسم الدواء
  dosage: "string",       // الجرعة
  frequency: "string",    // التكرار
  timeWeekday: "HH:MM",   // وقت الأيام العادية
  timeWeekend: "HH:MM",   // وقت الويكند (اختياري)
  timeHoliday: "HH:MM",   // وقت الأجازات (اختياري)
  notes: "string",        // ملاحظات (اختياري)
  hasDifferentTimes: boolean, // هل يوجد أوقات مختلفة
  taken: boolean,         // هل تم التناول اليوم
  lastTaken: Date|null    // آخر وقت تناول
}
```

## الدوال المساعدة

### تحديد نوع اليوم
```javascript
function getCurrentDayType() {
  const today = new Date().getDay();
  return {
    isWeekend: today === 5 || today === 6,
    dayName: ['الأحد', 'الاثنين', ...][today]
  };
}
```

### الحصول على الوقت المناسب
```javascript
function getMedicationTime(medication) {
  if (isHoliday && medication.timeHoliday) return medication.timeHoliday;
  if (isWeekend && medication.timeWeekend) return medication.timeWeekend;
  return medication.timeWeekday;
}
```
