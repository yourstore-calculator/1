# 1
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>حاسبة النوافذ والأبواب</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Cairo&display=swap');
  body {
    font-family: 'Cairo', sans-serif;
    background: #f8f9fa;
    color: #212529;
    margin: 0; padding: 0;
    display: flex; flex-direction: column; align-items: center;
    min-height: 100vh;
  }
  header {
    width: 100%;
    background: #212529;
    color: #fff;
    padding: 15px 0;
    text-align: center;
    font-size: 1.8rem;
    font-weight: bold;
    letter-spacing: 2px;
    box-shadow: 0 4px 8px rgba(0,0,0,0.15);
  }
  header img {
    max-height: 50px;
    vertical-align: middle;
    margin-left: 10px;
  }
  main {
    width: 95%;
    max-width: 700px;
    background: white;
    box-shadow: 0 8px 24px rgba(0,0,0,0.12);
    border-radius: 12px;
    margin: 25px 0 50px;
    padding: 30px 25px 40px;
  }
  h2 {
    text-align: center;
    margin-bottom: 20px;
    font-weight: 700;
    color: #343a40;
  }
  label {
    display: block;
    margin: 15px 0 7px;
    font-weight: 600;
  }
  select, input[type="number"], input[type="text"] {
    width: 100%;
    padding: 8px 10px;
    border: 2px solid #ced4da;
    border-radius: 8px;
    font-size: 1rem;
    transition: border-color 0.3s ease;
  }
  select:focus, input[type="number"]:focus, input[type="text"]:focus {
    outline: none;
    border-color: #495057;
  }
  .checkbox-group {
    margin: 12px 0 15px;
  }
  .checkbox-group label {
    display: inline-block;
    margin-left: 12px;
    font-weight: 500;
    cursor: pointer;
  }
  button {
    margin-top: 20px;
    background: #212529;
    color: white;
    border: none;
    padding: 12px 20px;
    border-radius: 8px;
    font-weight: 700;
    font-size: 1.1rem;
    cursor: pointer;
    width: 100%;
    transition: background 0.3s ease;
  }
  button:hover {
    background: #343a40;
  }
  .results {
    margin-top: 25px;
    background: #e9ecef;
    border-radius: 10px;
    padding: 20px;
    font-size: 1.1rem;
    color: #212529;
    line-height: 1.5;
    white-space: pre-line;
  }
  .footer-buttons {
    display: flex;
    justify-content: space-between;
    margin-top: 15px;
  }
  .footer-buttons button {
    width: 48%;
  }
  /* Responsive */
  @media (max-width: 500px) {
    main {
      padding: 20px 15px 30px;
    }
    header {
      font-size: 1.5rem;
    }
  }
</style>
</head>
<body>

<header>
  <img src="https://via.placeholder.com/150x50?text=Logo" alt="شعار الشركة" />
  حاسبة النوافذ والأبواب
</header>

<main>
  <h2>حاسبة الأسعار</h2>
  <form id="calcForm">

    <label for="productType">نوع المنتج</label>
    <select id="productType" name="productType" required>
      <option value="">-- اختر النوع --</option>
      <option value="نافذة">نافذة</option>
      <option value="باب">باب</option>
      <option value="أبواب سحب">أبواب سحب</option>
    </select>

    <label for="productVariant">نوع المنتج الفرعي</label>
    <select id="productVariant" name="productVariant" required disabled>
      <option value="">-- اختر النوع الفرعي --</option>
    </select>

    <label for="length">الطول (متر)</label>
    <input type="number" id="length" name="length" min="0.1" step="0.01" required placeholder="مثال: 2.2" />

    <label for="width">العرض (متر)</label>
    <input type="number" id="width" name="width" min="0.1" step="0.01" required placeholder="مثال: 1" />

    <label for="quantity">الكمية</label>
    <input type="number" id="quantity" name="quantity" min="1" step="1" value="1" required />

    <div id="additionsContainer" style="display:none;">
      <div class="checkbox-group">
        <input type="checkbox" id="curtain" name="curtain" />
        <label for="curtain">إضافة ستارة داخلية</label>
      </div>
      <div class="checkbox-group" id="meshCheckboxGroup" style="display:none;">
        <input type="checkbox" id="mesh" name="mesh" />
        <label for="mesh">إضافة شبك</label>
      </div>
    </div>

    <div id="curtainSizeContainer" style="display:none;">
      <label>مقاس الستارة (متر)</label>
      <div style="display:flex; gap:10px;">
        <input type="number" id="curtainLength" name="curtainLength" min="0.1" step="0.01" placeholder="الطول" />
        <input type="number" id="curtainWidth" name="curtainWidth" min="0.1" step="0.01" placeholder="العرض" />
      </div>
    </div>

    <button type="submit">احسب السعر</button>
  </form>

  <div class="footer-buttons" style="display:none;" id="footerButtons">
    <button id="clearResults">مسح النتائج</button>
    <button id="saveResults">حفظ النتائج كملف نصي</button>
  </div>

  <pre class="results" id="results"></pre>
</main>

<script>
  const productVariants = {
    "نافذة": [
      { key: "دبل_جلاس_دبل_فريم_ثابت", label: "دبل جلاس دبل فريم - ثابت", pricePerMeter: 34, cbmFactor: 0.13 },
      { key: "دبل_جلاس_دبل_فريم_حركة", label: "دبل جلاس دبل فريم - حركة", pricePerMeter: 73, cbmFactor: 0.13 },
      { key: "دبل_جلاس_دبل_فريم_حركتين", label: "دبل جلاس دبل فريم - حركتين", pricePerMeter: 92, cbmFactor: 0.13 },

      { key: "دبل_جلاس_سنجل_فريم_ثابت", label: "دبل جلاس سنجل فريم - ثابت", pricePerMeter: 26, cbmFactor: 0.07 },
      { key: "دبل_جلاس_سنجل_فريم_حركة", label: "دبل جلاس سنجل فريم - حركة", pricePerMeter: 46, cbmFactor: 0.07 },
      { key: "دبل_جلاس_سنجل_فريم_حركتين", label: "دبل جلاس سنجل فريم - حركتين", pricePerMeter: 58, cbmFactor: 0.07 },

      { key: "سنجل_جلاس_سنجل_فريم_ثابت", label: "سنجل جلاس سنجل فريم - ثابت", pricePerMeter: 20, cbmFactor: 0.07 },
      { key: "سنجل_جلاس_سنجل_فريم_حركة", label: "سنجل جلاس سنجل فريم - حركة", pricePerMeter: 43, cbmFactor: 0.07 },
      { key: "سنجل_جلاس_سنجل_فريم_حركتين", label: "سنجل جلاس سنجل فريم - حركتين", pricePerMeter: 47, cbmFactor: 0.07 },

      { key: "نوافذ_السلايدنج", label: "نوافذ السلايدنج", pricePerMeter: null, cbmFactor: 0.13, fixedAdd: 10 },

      { key: "النوافذ_الكهربائية", label: "النوافذ الكهربائية", pricePerMeter: 102, cbmFactor: 0.13 },

      { key: "السكاي_لايت_بدون_المكينة", label: "سكاي لايت بدون المكينة", pricePerMeter: 56, cbmFactor: 0.13 },

      { key: "كارتن_وول_بانواعه", label: "كارتن وول بانواعه", pricePerMeter: 58, cbmFactor: 0.13 },
    ],
    "باب": [
      { key: "المنيوم", label: "باب المنيوم", pricePerMeter: 180, cbmFactor: 0.11 },
      { key: "WPC", label: "باب WPC", pricePerMeter: 230, cbmFactor: 0.11 },
      { key: "دورات_المياه", label: "باب دورات المياه", pricePerMeter: 120, cbmFactor: 0.11 },
      { key: "باب_المدخل", label: "باب المدخل", pricePerMeter: 440, cbmFactor: 0.20 },
      { key: "باب_الحديقة", label: "باب الحديقة", pricePerMeter: 260, cbmFactor: 0.20 },
      { key: "الابواب_المتحركة", label: "الأبواب المتحركة", pricePerMeter: null, cbmFactor: 0.13 },
    ],
    "أبواب سحب": [
      { key: "باب_سحب_منيوم", label: "باب سحب المنيوم", pricePerMeter: 250, cbmFactor: 0.13 },
      { key: "باب_سحب_WPC", label: "باب سحب WPC", pricePerMeter: 230, cbmFactor: 0.11 },
      { key: "باب_سحب_بلاستيك", label: "باب سحب بلاستيك", pricePerMeter: 280, cbmFactor: 0.13 },
      { key: "باب_سحب_خشب", label: "باب سحب خشب", pricePerMeter: 300, cbmFactor: 0.13 },
    ]
  };

  const curtainsPricePerMeter = 26; // بدون شحن للستارة فقط

  const productTypeSelect = document.getElementById('productType');
  const productVariantSelect = document.getElementById('productVariant');
  const lengthInput = document.getElementById('length');
  const widthInput = document.getElementById('width');
  const quantityInput = document.getElementById('quantity');
  const additionsContainer = document.getElementById('additionsContainer');
  const curtainCheckbox = document.getElementById('curtain');
  const meshCheckboxGroup = document.getElementById('meshCheckboxGroup');
  const meshCheckbox = document.getElementById('mesh');
  const curtainSizeContainer = document.getElementById('curtainSizeContainer');
  const curtainLengthInput = document.getElementById('curtainLength');
  const curtainWidthInput = document.getElementById('curtainWidth');
  const resultsPre = document.getElementById('results');
  const footerButtons = document.getElementById('footerButtons');
  const clearResultsBtn = document.getElementById('clearResults');
  const saveResultsBtn = document.getElementById('saveResults');

  let resultsData = [];

  function populateVariants() {
    const type = productTypeSelect.value;
    productVariantSelect.innerHTML = '<option value="">-- اختر النوع الفرعي --</option>';
    if (!type) {
      productVariantSelect.disabled = true;
      additionsContainer.style.display = 'none';
      curtainSizeContainer.style.display = 'none';
      return;
    }
    productVariantSelect.disabled = false;

    productVariants[type].forEach(v => {
      const option = document.createElement('option');
      option.value = v.key;
      option.textContent = v.label;
      productVariantSelect.appendChild(option);
    });

    additionsContainer.style.display = 'none';
    curtainSizeContainer.style.display = 'none';
    curtainCheckbox.checked = false;
    meshCheckbox.checked = false;
    meshCheckboxGroup.style.display = 'none';
  }

  function handleAdditionsVisibility() {
    const type = productTypeSelect.value;
    const variantKey = productVariantSelect.value;

    // إظهار خيارات الإضافات فقط حسب شروطك

    // عرض الستارة فقط لنوافذ دبل جلاس و الابواب المتحركة و أبواب السحب
    if (
      (type === "نافذة" && variantKey.startsWith("دبل_جلاس")) ||
      (type === "باب" && variantKey === "الابواب_المتحركة") ||
      (type === "أبواب سحب")
    ) {
      additionsContainer.style.display = 'block';
    } else {
      additionsContainer.style.display = 'none';
      curtainCheckbox.checked = false;
      meshCheckbox.checked = false;
      curtainSizeContainer.style.display = 'none';
      meshCheckboxGroup.style.display = 'none';
    }

    // عرض الشبك فقط لنوافذ دبل جلاس و الأبواب المتحركة و أبواب السحب
    if (
      (type === "نافذة" && variantKey.startsWith("دبل_جلاس")) ||
      (type === "باب" && variantKey === "الابواب_المتحركة") ||
      (type === "أبواب سحب")
    ) {
      meshCheckboxGroup.style.display = 'block';
    } else {
      meshCheckboxGroup.style.display = 'none';
      meshCheckbox.checked = false;
    }
  }

  function handleCurtainSizeVisibility() {
    if (curtainCheckbox.checked) {
      curtainSizeContainer.style.display = 'block';
      curtainLengthInput.required = true;
      curtainWidthInput.required = true;
    } else {
      curtainSizeContainer.style.display = 'none';
      curtainLengthInput.required = false;
      curtainWidthInput.required = false;
    }
  }

  function resetForm() {
    resultsData = [];
    resultsPre.textContent = '';
    footerButtons.style.display = 'none';
  }

  productTypeSelect.addEventListener('change', () => {
    populateVariants();
    resetForm();
  });

  productVariantSelect.addEventListener('change', () => {
    handleAdditionsVisibility();
    resetForm();

    // لو النوع من الابواب WPC أو المنيوم أو دورات مياه خلي المقاس ثابت 2.2 طول و 1 عرض
    if (productTypeSelect.value === "باب") {
      const fixedTypes = ["المنيوم", "WPC", "دورات_المياه"];
      if (fixedTypes.includes(productVariantSelect.value)) {
        lengthInput.value = 2.2;
        widthInput.value = 1;
        lengthInput.readOnly = true;
        widthInput.readOnly = true;
      } else {
        lengthInput.readOnly = false;
        widthInput.readOnly = false;
      }
    } else {
      lengthInput.readOnly = false;
      widthInput.readOnly = false;
    }
  });

  curtainCheckbox.addEventListener('change', () => {
    handleCurtainSizeVisibility();
  });

  document.getElementById('calcForm').addEventListener('submit', function(e) {
    e.preventDefault();

    // قراءة البيانات
    const type = productTypeSelect.value;
    const variantKey = productVariantSelect.value;
    const variantData = productVariants[type].find(v => v.key === variantKey);
    const length = parseFloat(lengthInput.value);
    const width = parseFloat(widthInput.value);
    const quantity = parseInt(quantityInput.value, 10);

    if (!variantData) {
      alert("يرجى اختيار نوع فرعي صحيح");
      return;
    }

    // حساب المساحة وطول المحيط
    const area = length * width;
    const perimeter = 2 * (length + width);

    // حساب سعر المنتج (بالريال)
    let pricePerMeter = variantData.pricePerMeter;
    if (pricePerMeter === null) {
      alert("هذا النوع لا يحتوي على سعر محدد");
      return;
    }

    // حساب الشحن حسب الفاكتور الخاص بالنوع
    const cbmFactor = variantData.cbmFactor;
    const cbm = area * cbmFactor;
    const shippingCostPerItem = cbm * 48;

    // السعر بدون شحن (سعر للمتر × محيط)
    let basePricePerItem = pricePerMeter * perimeter;

    // السعر النهائي لكل قطعة مع الشحن
    let finalPricePerItem = basePricePerItem + shippingCostPerItem;

    // إضافة الستارة إن وجدت (سعر الستارة × مساحتها)
    let curtainCost = 0;
    if (curtainCheckbox.checked) {
      const cLength = parseFloat(curtainLengthInput.value);
      const cWidth = parseFloat(curtainWidthInput.value);
      if (isNaN(cLength) || isNaN(cWidth) || cLength <= 0 || cWidth <= 0) {
        alert("يرجى إدخال مقاس صحيح للستارة");
        return;
      }
      curtainCost = curtainsPricePerMeter * (cLength * cWidth);
      // لا يضاف شحن على الستارة حسب طلبك
    }

    // إضافة الشبك إذا تم اختيارها (افتراضياً سعره 50 ريال لكل قطعة)
    let meshCost = 0;
    if (meshCheckbox.checked) {
      meshCost = 50; // قيمة ثابتة مثال، يمكن تعديلها حسب السعر الحقيقي
    }

    // السعر النهائي مع الإضافات
    let totalPricePerItem = finalPricePerItem + curtainCost + meshCost;

    // السعر النهائي للكمية
    let totalPrice = totalPricePerItem * quantity;

    // تجميع نص النتيجة
    const resultText = `
النوع: ${variantData.label}
المقاس: ${length.toFixed(2)} × ${width.toFixed(2)} متر
الكمية: ${quantity}
----------------------------
سعر الوحدة بدون شحن: ${basePricePerItem.toFixed(2)} ريال
سعر الشحن لكل وحدة: ${shippingCostPerItem.toFixed(2)} ريال
سعر الستارة (إن وجدت): ${curtainCost.toFixed(2)} ريال
سعر الشبك (إن وجد): ${meshCost.toFixed(2)} ريال
----------------------------
السعر النهائي لكل وحدة (شحن + إضافات): ${totalPricePerItem.toFixed(2)} ريال
السعر النهائي للكمية: ${totalPrice.toFixed(2)} ريال
----------------------------
`;

    resultsData.push(resultText);
    resultsPre.textContent = resultsData.join('\n\n');
    footerButtons.style.display = 'flex';
  });

  clearResultsBtn.addEventListener('click', () => {
    resultsData = [];
    resultsPre.textContent = '';
    footerButtons.style.display = 'none';
  });

  saveResultsBtn.addEventListener('click', () => {
    if (!resultsData.length) return;

    const blob = new Blob([resultsData.join('\n\n')], { type: 'text/plain;charset=utf-8' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'نتائج-حاسبة-الأسعار.txt';
    a.click();
    URL.revokeObjectURL(url);
  });

  // تهيئة
  populateVariants();
</script>

</body>
</html>
