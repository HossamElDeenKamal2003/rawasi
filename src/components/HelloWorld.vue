<script setup>
import { ref, onMounted } from 'vue'
import emailjs from '@emailjs/browser'

const bankAccount = ref('0446050653330015')
const adminPhone = ref('97030922')
// EmailJS configuration
const EMAILJS_CONFIG = {
  publicKey: 'Y0fa_Z7Yp9zNN6N77',
  serviceId: 'service_5nin87d',
  templateId: 'template_jzjsnd5'
}

emailjs.init(EMAILJS_CONFIG.publicKey)

const toasts = ref([])

const showToast = (message, type = 'success') => {
  const id = Date.now()
  toasts.value.push({ id, message, type })
  setTimeout(() => {
    toasts.value = toasts.value.filter(t => t.id !== id)
  }, 3000)
}

const showOrderModal = ref(false)
const selectedService = ref(null)
const userPhone = ref('')
const userName = ref('')
const metersCount = ref('')
const isSubmitting = ref(false)

// Implementation Packages (باقات التنفيذ)
const implementationPackages = ref([
  {
    name: '🎨 تصميم داخلي',
    price: 1.500,
    currency: 'ر.ع',
    desc: 'تصميم داخلي احترافي مع كامل الخرائط',
    icon: '🎨',
    features: [
      'تصميم داخلي 3D مع مرتين تعديل',
      'خرائط توزيع الكهرباء',
      'خرائط توزيع الاثاث',
      'خرائط تفاصيل الديكور',
      'خرائط وجداول المواد المستخدمه في الديكور',
      'توزيع الإناره للطابقين وتوضيح أنواع الانارات المستخدمه'
    ]
  },
  {
    name: '🏠 تنفيذ الأسقف (كامل البيت)',
    price: 5.950,
    currency: 'ر.ع',
    desc: 'تنفيذ كامل لأسقف البيت مع المواد',
    icon: '🏗️',
    features: [
      'جيبسوم بورد عماني',
      'حديد تركيب عماني',
      'جيبسوم بورد مقاوم للرطوبه لدورات المياه',
      'جيبسوم بورد مقاوم للحراره للمطابخ والبانتري',
      'حديد لاتزيد المسافه عن 55 سم عماني',
      'عماله مصريه او باكستانيه فقط',
      'يشمل تركيب الجيبسوم بورد',
      'عمل فتحات الاناره حسب التصميم',
      'عمل التشطيب بالمعجون للفواصل',
      'ضمان التركيب بأعلى جوده تحت إشراف هندسي مستمر ومتكامل',
      'خبره في التركيب والتنفيذ'
    ]
  },
  {
    name: '✨ باقة التنفيذ ستاندر (أسقف + جدران)',
    price: 9.900,
    currency: 'ر.ع',
    desc: 'تنفيذ كامل للأسقف والجدران',
    icon: '⭐',
    features: [
      'يشمل جميع ماسبق للسقف',
      'جميع مواد الديكور للجدران من الجيبسوم بورد حسب التصميم مع عمل جزء سفلي مضاد للرطوبه',
      'جميع الديكورات من بديل خشب او بديل رخام',
      'عمل ديكورات الجدران من الاكريليك حسب التصميم'
    ]
  },
  {
    name: '💎 باقة التنفيذ بريميوم',
    price: 11.900,
    currency: 'ر.ع',
    desc: 'تنفيذ متكامل مع متابعة ميدانية وتركيب',
    icon: '💎',
    features: [
      'يشمل جميع ماسبق للسقف',
      'عمل زيارات ومتابعه ميدانيه قبل البلاستر لوضع اماكن الانارات والسوكيتات',
      'جميع مواد الديكور للجدران من الجيبسوم بورد مع جزء سفلي مضاد للرطوبه',
      'جميع الديكورات من بديل خشب او بديل رخام',
      'عمل ديكورات الجدران من الاكريليك حسب التصميم',
      'توفير وتركيب وايرات الانارات للسقف من نوع نحاس عماني',
      'تركيب جميع انواع الانارات (سبوت لايت - بروفايل لايت - تراك لايت)',
      'التوجيه والمساعده في شراء الانارات المناسبه حسب الشده والكلفن',
      'توفير وتركيب اصباغ السقف نوع جوتن لجميع مناطق التنزيل'
    ]
  },
  // {
  //   name: '🌿 تصميم حديقة داخلية',
  //   price: 0.500,
  //   currency: 'ر.ع',
  //   desc: 'تصميم احترافي للحدائق الداخلية',
  //   icon: '🌿',
  //   priceNote: '500 بيسة للمتر',
  //   features: [
  //     'التصميم 3D مع التعديل',
  //     'خرائط التنفيذ',
  //     'خرائط الانارات',
  //     'خرائط توزيع الكهرباء',
  //     'خرائط نظام توزيع الري'
  //   ]
  // }
])

// Design Packages (باقات التصميم)
const designPackages = ref([
  {
    name: '💎 باقة الفخامة',
    price: 2.950,
    currency: 'ر.ع',
    desc: 'الخيار الأمثل لمن يبحث عن الكمال والرفاهية',
    icon: '💎',
    popular: false,
    features: [
      'دراسة حركة الشمس و اتجاهات الرياح',
      'الرؤية الشاملة 3D: تصميم داخلي ثلاثي الأبعاد لـ كامل مساحات المشروع',
      'التميز الخارجي: تصميم واجهات 3D مع تصميم السور الخارجي واللاندسكيب',
      'المخططات الهندسية: (اسكتش معماري، واجهات 2D، تصميم إنشائي، خرائط الكهرباء)',
      'التفاصيل الفنية: مقاطع معمارية دقيقة',
      'الاعتماد: تشمل اعتماد البلدية'
    ]
  },
  {
    name: '✨ باقة رواق',
    price: 1.900,
    currency: 'ر.ع',
    desc: 'التوازن الذكي بين التصميم الهندسي والجمال الداخلي',
    icon: '✨',
    popular: true,
    features: [
      'دراسة حركة الشمس و اتجاهات الرياح',
      'لمسات فنية 3D: تصميم داخلي ثلاثي الأبعاد لأهم الفراغات',
      'الواجهة الأمامية: تصميم 3D للواجهة الرئيسية للمنزل',
      'المخططات الهندسية: (اسكتش معماري، واجهات 2D، تصميم إنشائي، خرائط الكهرباء)',
      'التفاصيل الفنية: مقطع معماري',
      'الاعتماد: تشمل اعتماد البلدية'
    ]
  },
  {
    name: '🏗️ الباقة الأساسية',
    price: 1.200,
    currency: 'ر.ع',
    desc: 'الأساس المتين لمشروعك مع خرائط هندسية معتمدة',
    icon: '🏗️',
    popular: false,
    features: [
      'دراسة حركة الشمس و اتجاهات الرياح',
      'المخططات الهندسية: (اسكتش معماري، واجهات 2D، تصميم إنشائي، خرائط الكهرباء)',
      'التفاصيل الفنية: مقطع معماري',
      'الاعتماد: تشمل اعتماد البلدية'
    ]
  }
])

// ALL Services (الخدمات الـ 9 كاملة)
const services = ref([
  { 
    name: '📞 استشارة أونلاين', 
    price: 10, 
    currency: 'ر.ع',
    desc: 'استشارة هندسية عبر الهاتف أو Zoom',
    icon: '💻',
    features: ['استشارة فورية', 'مدة 30 دقيقة', 'حلول سريعة'],
    needsMeters: false
  },
  { 
    name: '🏢 استشارة في المكتب', 
    price: 35, 
    currency: 'ر.ع',
    desc: 'استشارة متخصصة في مكتبنا',
    icon: '🏢',
    features: ['مدة ساعة', 'خرائط أولية', 'نقاش معمق'],
    needsMeters: false
  },
  { 
    name: '📍 زيارة موقع + تقرير فني', 
    price: 50, 
    currency: 'ر.ع',
    desc: 'داخل مسقط وبركاء',
    icon: '📍',
    features: ['معاينة الموقع', 'تقرير مفصل', 'توصيات فنية'],
    note: 'خارج مسقط وبركاء: 95 ر.ع',
    needsMeters: false
  },
  { 
    name: '🗺️تصميم حدائق و لاند سكيب', 
    price: .500, 
    currency: 'ر.ع',
    desc: 'خرائط معمارية احترافية',
    icon: '📐',
    features: ['500بيسة  للمتر', 'التصميم 3D مع التعديل', 'خرائط التنفيذ', 'خرائط الانارات', 'خرائط توزيع الكهرباء', 'خرائط نظام توزيع الري'],
    needsMeters: true,
    pricePerMeter: 1
  },
  { 
    name: '🎨 تصميم ثلاثي الأبعاد', 
    price: 3, 
    currency: 'ر.ع',
    desc: 'تصاميم واقعية ثلاثية الأبعاد',
    icon: '🎨',
    features: ['3 ريال للمتر', 'تصاميم احترافية', 'معاينة واقعية'],
    needsMeters: true,
    pricePerMeter: 3
  },
  { 
    name: '🏗️ تصميم مقاولات البناء', 
    price: 95, 
    currency: 'ر.ع',
    desc: 'هندسة وتصميم للمقاولات',
    icon: '🏗️',
    features: ['95 ريال للمتر', 'مخططات كاملة', 'كميات وجداول'],
    needsMeters: true,
    pricePerMeter: 95
  },
  { 
    name: '🛠️ ترميم', 
    price: 'حسب الحالة',
    currency: '',
    desc: 'خدمات ترميم شاملة',
    icon: '🔧',
    features: ['تقييم عبر الهاتف', 'معاينة الموقع', 'عرض سعر مجاني'],
    needsMeters: false
  },
  { 
    name: '📐 تطوير وتوسعة', 
    price: 'حسب المساحة',
    currency: '',
    desc: 'توسعة وتطوير المباني',
    icon: '📏',
    features: ['استشارة مبدئية', 'تصاميم توسعة', 'إشراف على التنفيذ'],
    needsMeters: true,
    pricePerMeter: 'حسب الاتفاق'
  },
  { 
    name: '✨ تشطيبات', 
    price: 'حسب المواصفات',
    currency: '',
    desc: 'تشطيبات داخلية وخارجية',
    icon: '🎨',
    features: ['خامات فاخرة', 'تنفيذ احترافي', 'إشراف هندسي'],
    needsMeters: false
  }
])

const calculateTotalPrice = () => {
  if (!selectedService.value || !metersCount.value) return selectedService.value?.price || 0
  if (selectedService.value.pricePerMeter && typeof selectedService.value.pricePerMeter === 'number') {
    return (selectedService.value.pricePerMeter * parseFloat(metersCount.value)).toFixed(2)
  }
  return selectedService.value.price
}

const openOrderModal = (service) => {
  selectedService.value = service
  userPhone.value = ''
  userName.value = ''
  metersCount.value = ''
  showOrderModal.value = true
}

const sendEmailViaEmailJS = async (orderData) => {
  try {
    const templateParams = {
      to_email: 'engmohamedkamal360@gmail.com',
      from_name: orderData.userName,
      user_phone: orderData.userPhone,
      service_name: orderData.serviceName,
      service_price: orderData.servicePrice,
      meters: orderData.meters || 'غير مطلوب',
      total_price: orderData.totalPrice,
      message: orderData.message,
      order_date: new Date().toLocaleString('ar-OM', { timeZone: 'Asia/Muscat' })
    }

    const response = await emailjs.send(
      EMAILJS_CONFIG.serviceId,
      EMAILJS_CONFIG.templateId,
      templateParams
    )
    return { success: true, response }
  } catch (error) {
    console.error('EmailJS Error:', error)
    return { success: false, error }
  }
}

const submitOrder = async () => {
  if (!userName.value || !userPhone.value) {
    showToast('❌ الرجاء إدخال الاسم ورقم الهاتف', 'error')
    return
  }

  if (selectedService.value.needsMeters && !metersCount.value) {
    showToast('❌ الرجاء إدخال عدد الأمتار', 'error')
    return
  }

  isSubmitting.value = true

  const totalPrice = selectedService.value.needsMeters && metersCount.value 
    ? calculateTotalPrice() 
    : selectedService.value.price

  const orderData = {
    userName: userName.value,
    userPhone: userPhone.value,
    serviceName: selectedService.value.name,
    servicePrice: selectedService.value.price,
    meters: metersCount.value,
    totalPrice: totalPrice,
    message: `
      🏗️ طلب خدمة جديد
      
      📋 الخدمة: ${selectedService.value.name}
      👤 الاسم: ${userName.value}
      📞 الهاتف: ${userPhone.value}
      ${selectedService.value.needsMeters ? `📏 عدد الأمتار: ${metersCount.value} م²` : ''}
      💰 السعر: ${totalPrice} ${selectedService.value.currency || 'ر.ع'}
    `
  }

  const emailResult = await sendEmailViaEmailJS(orderData)
  
  if (emailResult.success) {
    showToast('✅ تم إرسال طلبك بنجاح! سنتواصل معك قريباً', 'success')
    setTimeout(() => {
      showToast(`💳 بيانات التحويل البنكي:\n${bankAccount.value}\nالمبلغ: ${totalPrice} ر.ع`, 'info')
    }, 1000)
    
    showOrderModal.value = false
    userName.value = ''
    userPhone.value = ''
    metersCount.value = ''
    selectedService.value = null
  } else {
    showToast('❌ حدث خطأ في إرسال الطلب', 'error')
  }
  
  isSubmitting.value = false
}

const copyBankAccount = () => {
  navigator.clipboard.writeText(bankAccount.value)
  showToast('📋 تم نسخ رقم الحساب البنكي', 'success')
}

const copyAdminPhone = () => {
  navigator.clipboard.writeText(adminPhone.value)
  showToast('📋 تم نسخ رقم هاتف التحويل', 'success')
}

onMounted(() => {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('visible')
      }
    })
  }, { threshold: 0.1 })
  
  document.querySelectorAll('.impl-card, .package-card, .service-card').forEach(el => {
    observer.observe(el)
  })
})
</script>

<template>
  <div class="app">
    <!-- Toast -->
    <div class="toast-container">
      <div v-for="toast in toasts" :key="toast.id" :class="['toast', `toast-${toast.type}`]">
        <div class="toast-icon">
          <svg v-if="toast.type === 'success'" viewBox="0 0 24 24" fill="none">
            <path d="M20 6L9 17L4 12" stroke="currentColor" stroke-width="2"/>
          </svg>
          <svg v-else-if="toast.type === 'error'" viewBox="0 0 24 24" fill="none">
            <path d="M18 6L6 18M6 6L18 18" stroke="currentColor" stroke-width="2"/>
          </svg>
          <svg v-else viewBox="0 0 24 24" fill="none">
            <path d="M12 8V12M12 16H12.01M12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22Z" stroke="currentColor" stroke-width="2"/>
          </svg>
        </div>
        <div class="toast-message">{{ toast.message }}</div>
      </div>
    </div>

    <!-- Navigation -->
    <nav class="navbar">
      <div class="nav-container">
        <div class="logo">
          <div class="logo-mark">
            <svg width="32" height="32" viewBox="0 0 32 32" fill="none">
              <rect width="32" height="32" rx="8" fill="#14b8a6"/>
              <path d="M16 8L20 12L16 16L12 12L16 8Z" fill="white"/>
            </svg>
          </div>
          <span class="logo-text">eng.kamal.360 - <span>رواسي الهندسية</span></span>
        </div>
      </div>
    </nav>

    <!-- Hero -->
    <!-- Hero -->
<section class="hero" style="margin-top: 50px;">
  <div class="hero-container">
    <!-- اللوجو بقى فوق -->
    <div class="hero-logo">
      <img src="../../public/ChatGPT Image 8 أبريل 2026، 03_25_18 م.png" alt="رواسي الهندسية">
    </div>
    
    <div class="hero-badge">
      <span class="badge-dot"></span>
      خبرة أكثر من 10 سنوات
    </div>
    <h1 class="hero-title">
      نبني مستقبلك<br>
      <span class="title-highlight">بخبرة واحترافية</span>
    </h1>
    <p class="hero-subtitle">نقدم خدمات هندسية متكاملة من التصميم إلى التنفيذ بأعلى معايير الجودة</p>
    <div class="hero-stats">
      <div class="stat-item">
        <div class="stat-number">200+</div>
        <div class="stat-label">مشروع منفذ</div>
      </div>
      <div class="stat-divider"></div>
      <div class="stat-item">
        <div class="stat-number">150+</div>
        <div class="stat-label">عميل سعيد</div>
      </div>
      <div class="stat-divider"></div>
      <div class="stat-item">
        <div class="stat-number">24/7</div>
        <div class="stat-label">دعم فني</div>
      </div>
    </div>
      <div class="hero-whatsapp">
      <a href="https://api.whatsapp.com/send?phone=96897030921" target="_blank" class="whatsapp-btn">
        تواصل الآن عبر واتساب
         <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
          <path d="M12 2C6.48 2 2 6.48 2 12C2 17.52 6.48 22 12 22C17.52 22 22 17.52 22 12C22 6.48 17.52 2 12 2ZM12 20C7.58 20 4 16.42 4 12C4 7.58 7.58 4 12 4C16.42 4 20 7.58 20 12C20 16.42 16.42 20 12 20Z" fill="currentColor"/>
          <path d="M16.5 12.5C16.5 12.5 15.5 12.5 15 13C14.5 13.5 13.5 15 12.5 15C11.5 15 11 13.5 10.5 13C10 12.5 8.5 11.5 8.5 11.5L8 10.5C8 10.5 9 9.5 9.5 9C10 8.5 10.5 8 10.5 8C11 7.5 11 6.5 11 6.5C11 6.5 10 6 9.5 6C9 6 8 6.5 7.5 7C7 7.5 6 9 6 11C6 13 8 16.5 10 18C12 19.5 14 20 15.5 19.5C16.5 19 17.5 18 17.5 17C17.5 16 17 15 16.5 14.5C16 14 15.5 13.5 15 13.5C14.5 13.5 16.5 12.5 16.5 12.5Z" fill="currentColor"/>
        </svg>
      </a>
    </div>
  </div>
</section>
  <section class="services">
      <div class="container">
        <div class="section-header">
          <span class="section-subtitle">خدماتنا المتنوعة</span>
          <h2 class="section-title">نقدم لك</h2>
          <p class="section-desc">خدمات هندسية متنوعة تناسب جميع احتياجاتك</p>
        </div>
        <div class="services-grid">
          <div v-for="(service, index) in services" :key="index" class="service-card">
            <div class="service-icon">{{ service.icon }}</div>
            <h3 class="service-name">{{ service.name }}</h3>
            <p class="service-desc">{{ service.desc }}</p>
            <div class="service-price" v-if="service.price !== 'حسب الحالة' && service.price !== 'حسب المساحة' && service.price !== 'حسب المواصفات'">
              <span class="price-amount">{{ service.price }}</span>
              <span class="price-unit">{{ service.currency }}</span>
              <span v-if="service.priceAlt" class="price-note">/ {{ service.priceAlt }} بيسة/م²</span>
              <span v-else-if="service.needsMeters" class="price-note">/ المتر</span>
            </div>
            <div class="service-price variable" v-else>
              <span class="price-amount">{{ service.price }}</span>
            </div>
            <div class="service-note" v-if="service.note">💡 {{ service.note }}</div>
            <div class="service-features">
              <span v-for="(feature, idx) in service.features" :key="idx" class="feature-chip">{{ feature }}</span>
            </div>
            <button class="service-order" @click="openOrderModal(service)">اطلب الخدمة →</button>
          </div>
        </div>
      </div>
    </section>
    <!-- باقات التنفيذ والتصميم الداخلي -->
    <section class="implementation">
      <div class="container">
        <div class="section-header">
          <span class="section-subtitle">باقات التنفيذ والتصميم الداخلي</span>
          <h2 class="section-title">باقات التصميم</h2>
        </div>
        <div class="impl-grid">
          <div v-for="(pkg, index) in implementationPackages" :key="index" class="impl-card">
            <div class="impl-icon">{{ pkg.icon }}</div>
            <h3 class="impl-name">{{ pkg.name }}</h3>
            <p class="impl-desc">{{ pkg.desc }}</p>
            <div class="impl-price">
              <span class="price-amount">{{ pkg.price }}</span>
              <span class="price-unit">{{ pkg.currency }}</span>
              <span class="price-period">/ المتر</span>
              <span v-if="pkg.priceNote" class="price-note">{{ pkg.priceNote }}</span>
            </div>
            <div class="impl-features">
              <div v-for="(feature, idx) in pkg.features.slice(0, 4)" :key="idx" class="impl-feature">
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                  <path d="M20 6L9 17L4 12" stroke="#14b8a6" stroke-width="2"/>
                </svg>
                <span>{{ feature }}</span>
              </div>
              <div v-if="pkg.features.length > 4" class="impl-feature more">
                <span>+ {{ pkg.features.length - 4 }} ميزات أخرى</span>
              </div>
            </div>
            <button class="impl-order" @click="openOrderModal({...pkg, needsMeters: true, pricePerMeter: pkg.price})">اطلب الباقة →</button>
          </div>
        </div>
      </div>
    </section>

    <!-- باقات التصميم الهندسي -->
    <section class="packages">
      <div class="container">
        <div class="section-header">
          <span class="section-subtitle">باقات التصميم الهندسي</span>
          <h2 class="section-title">باقات الديكور</h2>
        </div>
        <div class="packages-grid">
          <div v-for="(pkg, index) in designPackages" :key="index" :class="['package-card', { popular: pkg.popular }]">
            <div class="popular-badge" v-if="pkg.popular">⭐ الأكثر طلباً</div>
            <div class="package-icon">{{ pkg.icon }}</div>
            <h3 class="package-name">{{ pkg.name }}</h3>
            <p class="package-desc">{{ pkg.desc }}</p>
            <div class="package-price">
              <span class="price-amount">{{ pkg.price }}</span>
              <span class="price-unit">{{ pkg.currency }}</span>
              <span class="price-period">/ المتر</span>
            </div>
            <div class="package-features">
              <div v-for="(feature, idx) in pkg.features.slice(0, 3)" :key="idx" class="package-feature">
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                  <path d="M20 6L9 17L4 12" stroke="#14b8a6" stroke-width="2"/>
                </svg>
                <span>{{ feature }}</span>
              </div>
              <div v-if="pkg.features.length > 3" class="package-feature more">
                <span>+ {{ pkg.features.length - 3 }} ميزات</span>
              </div>
            </div>
            <button class="package-order" @click="openOrderModal({...pkg, needsMeters: true, pricePerMeter: pkg.price})">اختر الباقة →</button>
          </div>
        </div>
      </div>
    </section>

    <!-- الخدمات الإضافية (الـ 9 كلها) -->
  

    <!-- Modal -->
    <div v-if="showOrderModal" class="modal" @click.self="showOrderModal = false">
      <div class="modal-container">
        <div class="modal-header">
          <h3>📋 طلب خدمة: {{ selectedService?.name }}</h3>
          <button class="modal-close" @click="showOrderModal = false">✕</button>
        </div>
        <div class="modal-body">
          <input type="text" v-model="userName" placeholder="👤 الاسم كاملاً" class="modal-input">
          <input type="tel" v-model="userPhone" placeholder="📞 رقم الهاتف" class="modal-input" dir="ltr">
          <div v-if="selectedService?.needsMeters">
            <input type="number" v-model="metersCount" placeholder="📏 عدد الأمتار (م²)" class="modal-input" step="0.01">
            <div class="price-preview" v-if="metersCount">💰 السعر الإجمالي: {{ calculateTotalPrice() }} {{ selectedService?.currency || 'ر.ع' }}</div>
          </div>
          <div class="bank-details">
            <div class="bank-header">💳 اسم صاحب الحساب </div>
            <code class="bank-number">Mohamed Kamal</code>
          </div>
          <div class="bank-details">
            <div class="bank-header">💳 للتحويل عبر رقم الحساب</div>
            <code class="bank-number">{{ bankAccount }}</code>
            <button class="copy-bank-btn" @click="copyBankAccount">📋 نسخ الحساب</button>
          </div>
        
          <div class="bank-details">
            <div class="bank-header">💳 رقم هاتف التحويل:</div>
            <code class="bank-number">{{ adminPhone }}</code>
            <button class="copy-bank-btn" @click="copyAdminPhone">📋 نسخ الهاتف</button>
          </div>
        </div>
        <div class="modal-footer">
          <button class="modal-submit" @click="submitOrder" :disabled="isSubmitting">{{ isSubmitting ? 'جاري الإرسال...' : 'إرسال الطلب' }}</button>
        </div>
      </div>
    </div>

    <!-- Footer -->
    <footer class="footer">
      <div class="container">
        <div class="footer-grid">
          <div class="footer-col">
            <div class="footer-logo">
              <span>🏗️ eng.kamal.360 - رواسي الهندسية</span>
            </div>
            <p>نقدم خدماتنا الهندسية في مسقط، بركاء، وجميع محافظات سلطنة عمان</p>
          </div>
          <div class="footer-col">
            <h4>معلومات الاتصال</h4>
            <p>📞 96897030921+</p>
            <p>📧 engmohamedkamal360@gmail.com</p>
            <p>📍 مسقط، سلطنة عمان</p>
          </div>
        </div>
        <div class="footer-bottom">© 2024 eng.kamal.360 - رواسي الهندسية</div>
      </div>
    </footer>
  </div>
</template>

<style scoped>
* { margin: 0; padding: 0; box-sizing: border-box; }

.app {
  font-family: 'Cairo', 'Tajawal', sans-serif;
  direction: rtl;
  background: #ffffff;
  color: #1a1a1a;
}

/* Toast */
.toast-container {
  position: fixed;
  top: 90px;
  left: 20px;
  z-index: 1000;
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.toast {
  background: white;
  border-radius: 16px;
  padding: 14px 20px;
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 300px;
  border-right: 4px solid #14b8a6;
  box-shadow: 0 10px 25px rgba(0,0,0,0.1);
  animation: slideIn 0.3s ease;
}
@keyframes slideIn {
  from { transform: translateX(-100%); opacity: 0; }
  to { transform: translateX(0); opacity: 1; }
}
.toast-icon svg { width: 20px; height: 20px; }
.toast-success .toast-icon { color: #14b8a6; }
.toast-error .toast-icon { color: #ef4444; }
.toast-info .toast-icon { color: #14b8a6; }
.toast-message { font-size: 0.875rem; color: #1a1a1a; white-space: pre-line; }

/* Navbar */
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  padding: 1rem 0;
  background: rgba(255,255,255,0.95);
  backdrop-filter: blur(20px);
  border-bottom: 1px solid rgba(20,184,166,0.2);
}
.nav-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 2rem;
}
.logo { display: flex; align-items: center; gap: 10px; }
.logo-text { font-size: 1.25rem; font-weight: 600; }
.logo-text span { color: #14b8a6; }

/* Hero */
.hero {
  min-height: 100vh;
  display: flex;
  align-items: center;
  background: linear-gradient(135deg, #f0fdfa 0%, #ffffff 100%);
}
.hero-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 2rem;
  text-align: center;
}
.hero-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  background: rgba(20,184,166,0.1);
  padding: 6px 16px;
  border-radius: 50px;
  color: #14b8a6;
  margin-bottom: 2rem;
}
.badge-dot {
  width: 6px;
  height: 6px;
  background: #14b8a6;
  border-radius: 50%;
  animation: pulse 2s infinite;
}
@keyframes pulse {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(1.2); }
}
.hero-title { font-size: 4rem; font-weight: 800; margin-bottom: 1.5rem; }
.title-highlight {
  background: linear-gradient(135deg, #14b8a6, #0d9488);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}
.hero-subtitle { font-size: 1.1rem; color: #666; max-width: 600px; margin: 0 auto 2rem; }
.hero-stats { display: flex; justify-content: center; gap: 3rem; padding-top: 2rem; border-top: 1px solid #e5e7eb; }
.stat-number { font-size: 2rem; font-weight: 700; color: #14b8a6; }
.stat-label { font-size: 0.8rem; color: #6b7280; }
.stat-divider { width: 1px; height: 40px; background: #e5e7eb; }

/* Common */
.container { max-width: 1200px; margin: 0 auto; padding: 0 2rem; }
.section-header { text-align: center; margin-bottom: 3rem; }
.section-subtitle {
  display: inline-block;
  background: rgba(20,184,166,0.1);
  padding: 4px 16px;
  border-radius: 50px;
  font-size: 0.8rem;
  color: #14b8a6;
  margin-bottom: 1rem;
}
.section-title { font-size: 2.5rem; font-weight: 700; margin-bottom: 0.5rem; }
.section-desc { color: #6b7280; }

/* Implementation Cards */
.implementation { padding: 5rem 0; background: #ffffff; }
.impl-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(350px, 1fr)); gap: 2rem; }
.impl-card {
  background: white;
  border-radius: 24px;
  padding: 1.8rem;
  border: 1px solid #e5e7eb;
  transition: all 0.3s;
  opacity: 0;
  transform: translateY(30px);
  transition: all 0.6s;
  display: flex;
  flex-direction: column;
}
.impl-card.visible { opacity: 1; transform: translateY(0); }
.impl-card:hover { transform: translateY(-5px); border-color: #14b8a6; box-shadow: 0 20px 40px rgba(0,0,0,0.08); }
.impl-icon { font-size: 2.5rem; margin-bottom: 1rem; text-align: center; }
.impl-name { font-size: 1.2rem; font-weight: 700; margin-bottom: 0.5rem; text-align: center; }
.impl-desc { font-size: 0.85rem; color: #6b7280; margin-bottom: 1rem; text-align: center; }
.impl-price { text-align: center; margin-bottom: 1rem; padding: 10px; background: #f0fdfa; border-radius: 16px; }
.impl-price .price-amount { font-size: 1.8rem; font-weight: 700; color: #14b8a6; }
.impl-price .price-unit { font-size: 0.8rem; color: #14b8a6; }
.impl-price .price-period { font-size: 0.7rem; color: #6b7280; }
.impl-price .price-note { display: block; font-size: 0.7rem; color: #6b7280; }
.impl-features { flex: 1; margin-bottom: 1rem; }
.impl-feature { display: flex; align-items: center; gap: 8px; font-size: 0.75rem; color: #4b5563; margin-bottom: 6px; }
.impl-feature.more { color: #14b8a6; font-weight: 600; margin-top: 8px; }
.impl-order {
  width: 100%;
  background: linear-gradient(135deg, #14b8a6, #0d9488);
  border: none;
  padding: 12px;
  border-radius: 16px;
  color: white;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
  margin-top: auto;
}
.impl-order:hover { transform: translateY(-2px); box-shadow: 0 10px 20px rgba(20,184,166,0.3); }

/* Packages Cards */
.packages { padding: 5rem 0; background: #f9fafb; }
.packages-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(320px, 1fr)); gap: 2rem; }
.package-card {
  position: relative;
  background: white;
  border-radius: 24px;
  padding: 1.8rem;
  border: 2px solid #e5e7eb;
  transition: all 0.3s;
  opacity: 0;
  transform: translateY(30px);
  transition: all 0.6s;
  display: flex;
  flex-direction: column;
}
.package-card.visible { opacity: 1; transform: translateY(0); }
.package-card:hover { transform: translateY(-8px); border-color: #14b8a6; }
.package-card.popular { border-color: #14b8a6; box-shadow: 0 10px 30px rgba(20,184,166,0.1); }
.popular-badge {
  position: absolute;
  top: -12px;
  left: 50%;
  transform: translateX(-50%);
  background: linear-gradient(135deg, #f59e0b, #ea580c);
  padding: 5px 15px;
  border-radius: 50px;
  font-size: 0.75rem;
  font-weight: 600;
  color: white;
}
.package-icon { font-size: 2.5rem; margin-bottom: 1rem; text-align: center; }
.package-name { font-size: 1.3rem; font-weight: 700; margin-bottom: 0.5rem; text-align: center; }
.package-desc { font-size: 0.85rem; color: #6b7280; margin-bottom: 1rem; text-align: center; }
.package-price { text-align: center; margin-bottom: 1rem; padding: 10px; background: #f0fdfa; border-radius: 16px; }
.package-price .price-amount { font-size: 1.8rem; font-weight: 700; color: #14b8a6; }
.package-features { flex: 1; margin-bottom: 1rem; }
.package-feature { display: flex; align-items: center; gap: 8px; font-size: 0.75rem; color: #4b5563; margin-bottom: 6px; }
.package-feature.more { color: #14b8a6; font-weight: 600; margin-top: 8px; }
.package-order {
  width: 100%;
  background: linear-gradient(135deg, #14b8a6, #0d9488);
  border: none;
  padding: 12px;
  border-radius: 16px;
  color: white;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
  margin-top: auto;
}
.package-order:hover { transform: translateY(-2px); box-shadow: 0 10px 20px rgba(20,184,166,0.3); }

/* Service Cards */
.services { padding: 5rem 0; background: #ffffff; }
.services-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(320px, 1fr)); gap: 2rem; }
.service-card {
  background: white;
  border-radius: 24px;
  padding: 1.5rem;
  border: 1px solid #e5e7eb;
  transition: all 0.3s;
  opacity: 0;
  transform: translateY(30px);
  transition: all 0.6s;
  display: flex;
  flex-direction: column;
}
.service-card.visible { opacity: 1; transform: translateY(0); }
.service-card:hover { transform: translateY(-5px); border-color: #14b8a6; box-shadow: 0 20px 40px rgba(0,0,0,0.08); }
.service-icon { font-size: 2rem; margin-bottom: 0.5rem; }
.service-name { font-size: 1.1rem; font-weight: 700; margin-bottom: 0.25rem; }
.service-desc { font-size: 0.8rem; color: #6b7280; margin-bottom: 0.75rem; }
.service-price { margin: 0.75rem 0; padding: 8px; background: #f0fdfa; border-radius: 12px; text-align: center; }
.service-price .price-amount { font-size: 1.5rem; font-weight: 700; color: #14b8a6; }
.service-price.variable .price-amount { font-size: 1rem; }
.price-note { font-size: 0.65rem; color: #6b7280; }
.service-note { background: #fef3c7; padding: 6px 10px; border-radius: 10px; font-size: 0.7rem; color: #d97706; margin: 0.5rem 0; }
.service-features { display: flex; flex-wrap: wrap; gap: 6px; margin: 0.75rem 0; flex: 1; }
.feature-chip { background: #f3f4f6; padding: 4px 10px; border-radius: 20px; font-size: 0.7rem; color: #4b5563; }
.service-order {
  width: 100%;
  background: linear-gradient(135deg, #14b8a6, #0d9488);
  border: none;
  padding: 10px;
  border-radius: 14px;
  color: white;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
  margin-top: auto;
}
.service-order:hover { transform: translateY(-2px); box-shadow: 0 10px 20px rgba(20,184,166,0.3); }

/* Modal */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  backdrop-filter: blur(5px);
}
.modal-container {
  background: white;
  border-radius: 24px;
  width: 90%;
  max-width: 450px;
  max-height: 90vh;
  overflow-y: auto;
}
.modal-header { padding: 1.5rem; border-bottom: 1px solid #e5e7eb; display: flex; justify-content: space-between; align-items: center; }
.modal-close { background: none; border: none; font-size: 1.5rem; cursor: pointer; color: #6b7280; }
.modal-body { padding: 1.5rem; display: flex; flex-direction: column; gap: 1rem; }
.modal-input { width: 100%; padding: 12px; background: #f9fafb; border: 1px solid #e5e7eb; border-radius: 12px; font-family: inherit; }
.modal-input:focus { outline: none; border-color: #14b8a6; }
.price-preview { margin-top: 8px; padding: 8px; background: #f0fdfa; border-radius: 8px; font-size: 0.85rem; color: #14b8a6; text-align: center; }
.bank-details { background: #f9fafb; padding: 1rem; border-radius: 16px; }
.bank-header { font-weight: 600; color: #14b8a6; margin-bottom: 8px; }
.bank-number { display: block; font-family: monospace; font-size: 0.8rem; background: white; padding: 8px; border-radius: 8px; margin-bottom: 8px; direction: ltr; text-align: left; }
.copy-bank-btn { background: rgba(20,184,166,0.1); border: none; padding: 6px 12px; border-radius: 8px; font-size: 0.7rem; color: #14b8a6; cursor: pointer; }
.modal-footer { padding: 1.5rem; border-top: 1px solid #e5e7eb; }
.modal-submit { width: 100%; background: linear-gradient(135deg, #14b8a6, #0d9488); border: none; padding: 14px; border-radius: 12px; color: white; font-weight: 600; cursor: pointer; }

/* Footer */
.footer { background: #f9fafb; padding: 3rem 0 1rem; border-top: 1px solid #e5e7eb; }
.footer-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 2rem; margin-bottom: 2rem; }
.footer-logo { margin-bottom: 1rem; font-weight: 600; font-size: 1.1rem; }
.footer-col p { color: #6b7280; font-size: 0.85rem; line-height: 1.6; }
.footer-col h4 { font-size: 1rem; margin-bottom: 1rem; color: #14b8a6; }
.footer-bottom { text-align: center; padding-top: 2rem; border-top: 1px solid #e5e7eb; color: #9ca3af; font-size: 0.8rem; }

@media (max-width: 768px) {
  .hero-title { font-size: 2.5rem; }
  .hero-stats { flex-direction: column; gap: 1rem; }
  .stat-divider { display: none; }
  .footer-grid { grid-template-columns: 1fr; text-align: center; }
}

.feature-chip{
  height: min-content;
}

/* Hero Logo */
.hero-logo {
  margin-bottom: 1.5rem;
}

.hero-logo img {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  object-fit: cover;
  border: 3px solid #14b8a6;
  box-shadow: 0 10px 30px rgba(20, 184, 166, 0.2);
}

/* Hero WhatsApp Button */
.hero-whatsapp {
  margin-top: 2.5rem;
}

.whatsapp-btn {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  background: linear-gradient(135deg, #25D366, #128C7E);
  color: white;
  padding: 12px 30px;
  border-radius: 50px;
  text-decoration: none;
  font-weight: 600;
  font-size: 1rem;
  transition: all 0.3s;
  box-shadow: 0 5px 15px rgba(37, 211, 102, 0.3);
}

.whatsapp-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 25px rgba(37, 211, 102, 0.4);
}

.whatsapp-btn svg {
  width: 22px;
  height: 22px;
}
</style>