
# ⚡ qdeeh Catcher

### High-Speed Network Discovery & Service Verification Tool

<p align="center">
  <strong>Discover. Verify. Identify.</strong>
</p>

---

## 🚀 مقدمة المشروع

هل تريد فهم كيفية اكتشاف الخدمات الشبكية والتحقق من استجابتها باستخدام Python وأدوات تحليل الشبكات؟

**qdeeh Catcher** هو مشروع لاستكشاف الخدمات الشبكية، يجمع بين الاتصالات المتوازية، والفحص الأولي للمنافذ، والتحقق من خدمات الويب، والاستفادة من Nmap لتحليل الخدمات التي تستجيب للفحص.

يعتمد المشروع على آلية متعددة المراحل تهدف إلى تجاوز الأهداف التي لا تستجيب مبكرًا، ثم إجراء فحوصات إضافية على الأهداف التي تجتاز المرحلة الأولى.

تم تطوير المشروع باستخدام Python مع دمج Nmap لتوفير معلومات إضافية عن الخدمات المكتشفة.

> **تنبيه:** صُمّم المشروع للاستخدام التعليمي واختبار الشبكات والأنظمة التي تملكها أو لديك تصريح صريح بفحصها. لا تستخدمه لفحص نطاقات عامة دون إذن.

---

## ✨ مميزات المشروع

- ⚡ **Multithreading:** تنفيذ مهام متعددة بالتوازي باستخدام `ThreadPoolExecutor`.
- 🌐 **Web Service Detection:** التحقق من استجابة خدمات HTTP وHTTPS.
- 🔌 **TCP Connectivity Check:** اختبار إمكانية الاتصال بالمنافذ المستهدفة باستخدام `socket`.
- 🔍 **Nmap Integration:** الاستفادة من Nmap لمحاولة تحديد الخدمات وإصداراتها.
- 🛡️ **False Positive Filtering:** استبعاد بعض النتائج التي تتطابق مع صفحات افتراضية أو رسائل رفض الوصول.
- 📡 **Multi-Stage Scanning:** تقسيم الفحص إلى مراحل لتقليل عمليات الفحص الإضافية للأهداف غير المستجيبة.
- 🖥️ **Terminal Output:** عرض النتائج وتقارير Nmap في الطرفية.
- 🎨 **ASCII Banner:** عرض شعار المشروع باستخدام مكتبة `pyfiglet`.

---

## 🧠 كيف يعمل qdeeh Catcher؟

ينفّذ البرنامج سلسلة من المراحل لفحص الأهداف المحددة في إعداداته.

### 1. توليد عناوين IP

يختار البرنامج نطاق CIDR من القائمة الموجودة في `IP_RANGES`، ثم يحاول توليد عنوان IP عشوائي منه.

هذه الآلية لا تضمن أن العنوان الناتج تابع لخدمة نشطة أو أن مالكه يسمح بفحصه؛ لذلك يجب ضبط القائمة لتشمل نطاقات مصرحًا باختبارها فقط.

### 2. فحص الاتصال الأولي

يستخدم البرنامج:

```python
socket.create_connection((ip, port), timeout=0.15)
```

لإجراء محاولة اتصال TCP بالمنفذ المحدد.

إذا فشل الاتصال ضمن المهلة المحددة، يتجاوز البرنامج الهدف ولا ينتقل إلى مراحل التحقق التالية.

### 3. التحقق من خدمات الويب

عند فحص منفذ مصنّف داخل البرنامج باعتباره منفذ ويب، يرسل طلب HTTP أو HTTPS باستخدام مكتبة `requests`.

بعد ذلك، يفحص البرنامج بعض المعلومات، منها:

- رمز استجابة HTTP.
- محتوى صفحة الويب.
- ترويسة `Server`.
- وجود كلمات قد تشير إلى صفحات افتراضية أو رسائل رفض الوصول.

هذه المؤشرات تساعد في تصفية بعض النتائج، لكنها لا تضمن تحديد الخدمة بصورة صحيحة في جميع الحالات.

### 4. تحليل الخدمة باستخدام Nmap

إذا اجتاز الهدف مراحل التحقق السابقة، يحاول البرنامج تشغيل Nmap باستخدام خيارات تتضمن:

| الخيار | الوظيفة |
|---|---|
| `-p` | تحديد المنفذ المطلوب فحصه |
| `-sV` | محاولة تحديد الخدمة وإصدارها |
| `-Pn` | تخطي اكتشاف المضيف التقليدي |
| `-T4` | استخدام إعداد زمني أسرع |
| `--open` | عرض المنافذ المفتوحة |
| `--host-timeout` | تحديد مهلة قصوى لمعالجة المضيف |

إذا احتوى تقرير Nmap على إشارة إلى منفذ مفتوح، يعرض البرنامج عنوان IP والمنفذ ومعلومات الخدمة والتقرير الناتج.

**ملاحظة:** نجاح الاتصال لا يثبت وحده هوية الخدمة، كما أن نتيجة Nmap ليست ضمانًا مطلقًا لصحة التصنيف.

---

## 🛠️ المتطلبات الأساسية

يحتاج المشروع إلى المتطلبات التالية:

| المتطلب | الاستخدام |
|---|---|
| Python 3 | تشغيل البرنامج |
| pip | تثبيت مكتبات Python |
| Nmap بأحدث إصدار مستقر | تحليل الخدمات والمنافذ |
| اتصال شبكي | الوصول إلى الأهداف المصرح بفحصها |
| Windows أو بيئة متوافقة | تشغيل البرنامج مع ضبط مسار Nmap الصحيح |

### 📌 متطلب أساسي: Nmap

يجب تثبيت أحدث إصدار مستقر من Nmap قبل تشغيل المشروع.

🔗 **الموقع الرسمي للتنزيل:**

https://nmap.org/download.html

بعد التثبيت، افتح PowerShell ونفّذ:

```powershell
nmap --version
```

إذا ظهر رقم الإصدار ومعلومات Nmap، فهذا يعني أن الطرفية تستطيع الوصول إلى الأداة.

إذا لم يتعرف النظام على الأمر، أضف مسار تثبيت Nmap إلى متغير البيئة `PATH` أو اضبط المسار في الكود.

يستخدم الكود الحالي المسار التالي:

```python
NMAP_PATH = r"C:\Program Files (x86)\Nmap\nmap.exe"
```

قد يختلف مسار التثبيت على جهازك؛ لذلك يجب التأكد من مطابقته للموقع الفعلي للملف التنفيذي.

**مهم:** تثبيت مكتبات Python لا يثبّت Nmap. يجب تثبيت Nmap بصورة مستقلة.

---

## 📥 تحميل المشروع

### الطريقة الأولى: باستخدام Git

افتح الطرفية ونفّذ:

```bash
git clone https://github.com/YOUR_USERNAME/qdeeh-catcher.git
cd qdeeh-catcher
```

استبدل `YOUR_USERNAME` باسم حساب GitHub الصحيح، وتأكد من أن عنوان المستودع واسم المجلد يطابقان مشروعك الفعلي.

### الطريقة الثانية: باستخدام ZIP

1. افتح مستودع المشروع على GitHub.
2. اضغط على زر `Code`.
3. اختر `Download ZIP`.
4. فك ضغط الملف.
5. افتح الطرفية داخل مجلد المشروع.

---

## 📦 تثبيت المتطلبات

### الخطوة الأولى: إنشاء بيئة افتراضية

على Windows باستخدام PowerShell:

```powershell
python -m venv .venv
```

فعّل البيئة الافتراضية:

```powershell
.\.venv\Scripts\Activate.ps1
```

إذا كنت تستخدم Command Prompt بدلًا من PowerShell:

```bat
.venv\Scripts\activate.bat
```

### الخطوة الثانية: تحديث pip

```bash
python -m pip install --upgrade pip
```

### الخطوة الثالثة: تثبيت مكتبات Python دفعة واحدة

أنشئ ملفًا باسم `requirements.txt` داخل مجلد المشروع، وأضف إليه:

```text
requests
urllib3
pyfiglet
```

ثم نفّذ:

```bash
python -m pip install -r requirements.txt
```

سيقوم pip بقراءة الملف وتثبيت المكتبات الخارجية المطلوبة.

المكتبات التالية لا تحتاج إلى تثبيت منفصل لأنها مدمجة مع Python:

- `random`
- `socket`
- `os`
- `subprocess`
- `concurrent.futures`

---

## ▶️ تشغيل المشروع

بعد تثبيت Python وNmap ومكتبات المشروع، تأكد من وجود ملف البرنامج في مجلد المشروع.

شغّله باستخدام:

```bash
python main.py
```

إذا كان اسم الملف مختلفًا عن `main.py`، استبدله باسم الملف الصحيح.

عند التشغيل، يعرض البرنامج الشعار النصي ثم يبدأ تنفيذ مهام الفحص وفق الإعدادات الموجودة في الكود.

### إيقاف التشغيل

لإيقاف البرنامج من الطرفية:

```text
Ctrl + C
```

---

## ⚙️ إعدادات المشروع

### عدد الخيوط المتوازية

يُحدّد عدد الخيوط عند استدعاء الدالة:

```python
run_turbo_scan(max_workers=500)
```

يمثل الرقم `500` الحد الأقصى للخيوط التي يمكن أن يديرها `ThreadPoolExecutor`، وليس ضمانًا لتحقيق سرعة محددة.

زيادة العدد قد ترفع استهلاك موارد الجهاز وتزيد الضغط على الشبكة. اختبر الإعدادات داخل بيئة تملكها، وابدأ بعدد منخفض مناسب لموارد النظام.

### نطاقات عناوين IP

توجد النطاقات في القائمة:

```python
IP_RANGES = [
    # نطاقات CIDR المصرح بفحصها
]
```

يجب أن تقتصر هذه القائمة على نطاقات تملكها أو لديك تصريح صريح بفحصها.

### المنافذ

تُحدّد المنافذ في `ports_pool` داخل الكود. وتشمل الإعدادات الحالية منافذ ويب ومنافذ لخدمات شبكية أخرى.

وجود منفذ ضمن القائمة لا يعني أن الخدمة متاحة، ولا يعني أن النظام المستهدف ضعيف أمنيًا.

---

## ⚠️ ملاحظات وقيود تقنية

- يستخدم الكود الحالي `verify=False` مع طلبات HTTPS، ما يعطّل التحقق من شهادات TLS. لا يُنصح بذلك عند الحاجة إلى التحقق الموثوق من هوية الخادم.
- تعطيل تحذيرات SSL يخفي التحذيرات، لكنه لا يعالج أسبابها.
- قائمة `FALSE_POSITIVE_KEYWORDS` لا تغطي جميع الحالات الممكنة، وقد تستبعد استجابات صحيحة أو تسمح بمرور نتائج غير دقيقة.
- نجاح اتصال TCP لا يثبت نوع الخدمة التي تعمل على المنفذ.
- قد تؤثر الجدران النارية والمهلات الزمنية ومحددات معدل الاتصالات في النتائج.
- يتجاهل الكود بعض الاستثناءات دون تسجيل تفاصيلها، مما قد يجعل تشخيص الأخطاء أصعب.
- يعتمد البرنامج على حلقة مستمرة لتوليد دفعات جديدة من المهام، وليس على مسح شامل ومنظم لكل عنوان ضمن النطاقات.
- لا يضمن استخدام عدد كبير من الخيوط أداءً أفضل؛ فالنتيجة تعتمد على موارد الجهاز والشبكة والأهداف التي يجري اختبارها.

---

## 🔐 الاستخدام المسؤول

يجب استخدام qdeeh Catcher ضمن نطاق مصرح به فقط.

- احصل على إذن واضح قبل فحص أي نظام لا تملكه.
- استخدم نطاقات اختبار محددة ومعروفة.
- لا ترفع معدل الاتصالات إلى مستوى يؤثر في استقرار الشبكة.
- التزم بالقوانين وسياسات الاستخدام المعمول بها.
- لا تعتبر المنافذ المفتوحة دليلًا على وجود ثغرة أمنية.

تقع مسؤولية الاستخدام الصحيح والحصول على التصاريح اللازمة على عاتق المستخدم.

---

## 🚀 أفكار للتطوير مستقبلًا

يمكن توسيع المشروع مستقبلًا بإضافة:

- 📄 تصدير النتائج إلى JSON وCSV.
- 📊 إحصائيات عن الأهداف المفحوصة والخدمات المكتشفة.
- 🧾 نظام Logging لتسجيل الأخطاء والنتائج.
- ⚙️ ملف إعدادات مستقل للنطاقات والمنافذ والمهلات.
- 🧪 اختبارات آلية للتحقق من وظائف البرنامج.
- 🖥️ واجهة رسومية لمتابعة النتائج.
- 🛡️ تحسين إدارة الاتصالات المتوازية وحدود معدل الفحص.
- 🔍 تحسين تصنيف الخدمات وتقليل النتائج الإيجابية والسلبية الخاطئة.

---

## 🤝 المساهمة في المشروع

نرحب بالمساهمات التي تساعد على تطوير المشروع وتحسين جودة الكود ودقة النتائج.

1. أنشئ Fork للمستودع.
2. أنشئ فرعًا جديدًا للتعديلات.
3. اختبر التعديلات داخل بيئة مصرح بها.
4. أرسل Pull Request مع شرح واضح للتغييرات.

---

## 📜 الترخيص
                                 Apache License
                           Version 2.0, January 2004
                        http://www.apache.org/licenses/

   TERMS AND CONDITIONS FOR USE, REPRODUCTION, AND DISTRIBUTION

   1. Definitions.

      "License" shall mean the terms and conditions for use, reproduction,
      and distribution as defined by Sections 1 through 9 of this document.

      "Licensor" shall mean the copyright owner or entity authorized by
      the copyright owner that is granting the License.

      "Legal Entity" shall mean the union of the acting entity and all
      other entities that control, are controlled by, or are under common
      control with that entity. For the purposes of this definition,
      "control" means (i) the power, direct or indirect, to cause the
      direction or management of such entity, whether by contract or
      otherwise, or (ii) ownership of fifty percent (50%) or more of the
      outstanding shares, or (iii) beneficial ownership of such entity.

      "You" (or "Your") shall mean an individual or Legal Entity
      exercising permissions granted by this License.

      "Source" form shall mean the preferred form for making modifications,
      including but not limited to software source code, documentation
      source, and configuration files.

      "Object" form shall mean any form resulting from mechanical
      transformation or translation of a Source form, including but
      not limited to compiled object code, generated documentation,
      and conversions to other media types.

      "Work" shall mean the work of authorship, whether in Source or
      Object form, made available under the License, as indicated by a
      copyright notice that is included in or attached to the work
      (an example is provided in the Appendix below).

      "Derivative Works" shall mean any work, whether in Source or Object
      form, that is based on (or derived from) the Work and for which the
      editorial revisions, annotations, elaborations, or other modifications
      represent, as a whole, an original work of authorship. For the purposes
      of this License, Derivative Works shall not include works that remain
      separable from, or merely link (or bind by name) to the interfaces of,
      the Work and Derivative Works thereof.

      "Contribution" shall mean any work of authorship, including
      the original version of the Work and any modifications or additions
      to that Work or Derivative Works thereof, that is intentionally
      submitted to Licensor for inclusion in the Work by the copyright owner
      or by an individual or Legal Entity authorized to submit on behalf of
      the copyright owner. For the purposes of this definition, "submitted"
      means any form of electronic, verbal, or written communication sent
      to the Licensor or its representatives, including but not limited to
      communication on electronic mailing lists, source code control systems,
      and issue tracking systems that are managed by, or on behalf of, the
      Licensor for the purpose of discussing and improving the Work, but
      excluding communication that is conspicuously marked or otherwise
      designated in writing by the copyright owner as "Not a Contribution."

      "Contributor" shall mean Licensor and any individual or Legal Entity
      on behalf of whom a Contribution has been received by Licensor and
      subsequently incorporated within the Work.

   2. Grant of Copyright License. Subject to the terms and conditions of
      this License, each Contributor hereby grants to You a perpetual,
      worldwide, non-exclusive, no-charge, royalty-free, irrevocable
      copyright license to reproduce, prepare Derivative Works of,
      publicly display, publicly perform, sublicense, and distribute the
      Work and such Derivative Works in Source or Object form.

   3. Grant of Patent License. Subject to the terms and conditions of
      this License, each Contributor hereby grants to You a perpetual,
      worldwide, non-exclusive, no-charge, royalty-free, irrevocable
      (except as stated in this section) patent license to make, have made,
      use, offer to sell, sell, import, and otherwise transfer the Work,
      where such license applies only to those patent claims licensable
      by such Contributor that are necessarily infringed by their
      Contribution(s) alone or by combination of their Contribution(s)
      with the Work to which such Contribution(s) was submitted. If You
      institute patent litigation against any entity (including a
      cross-claim or counterclaim in a lawsuit) alleging that the Work
      or a Contribution incorporated within the Work constitutes direct
      or contributory patent infringement, then any patent licenses
      granted to You under this License for that Work shall terminate
      as of the date such litigation is filed.

   4. Redistribution. You may reproduce and distribute copies of the
      Work or Derivative Works thereof in any medium, with or without
      modifications, and in Source or Object form, provided that You
      meet the following conditions:

      (a) You must give any other recipients of the Work or
          Derivative Works a copy of this License; and

      (b) You must cause any modified files to carry prominent notices
          stating that You changed the files; and

      (c) You must retain, in the Source form of any Derivative Works
          that You distribute, all copyright, patent, trademark, and
          attribution notices from the Source form of the Work,
          excluding those notices that do not pertain to any part of
          the Derivative Works; and

      (d) If the Work includes a "NOTICE" text file as part of its
          distribution, then any Derivative Works that You distribute must
          include a readable copy of the attribution notices contained
          within such NOTICE file, excluding those notices that do not
          pertain to any part of the Derivative Works, in at least one
          of the following places: within a NOTICE text file distributed
          as part of the Derivative Works; within the Source form or
          documentation, if provided along with the Derivative Works; or,
          within a display generated by the Derivative Works, if and
          wherever such third-party notices normally appear. The contents
          of the NOTICE file are for informational purposes only and
          do not modify the License. You may add Your own attribution
          notices within Derivative Works that You distribute, alongside
          or as an addendum to the NOTICE text from the Work, provided
          that such additional attribution notices cannot be construed
          as modifying the License.

      You may add Your own copyright statement to Your modifications and
      may provide additional or different license terms and conditions
      for use, reproduction, or distribution of Your modifications, or
      for any such Derivative Works as a whole, provided Your use,
      reproduction, and distribution of the Work otherwise complies with
      the conditions stated in this License.

   5. Submission of Contributions. Unless You explicitly state otherwise,
      any Contribution intentionally submitted for inclusion in the Work
      by You to the Licensor shall be under the terms and conditions of
      this License, without any additional terms or conditions.
      Notwithstanding the above, nothing herein shall supersede or modify
      the terms of any separate license agreement you may have executed
      with Licensor regarding such Contributions.

   6. Trademarks. This License does not grant permission to use the trade
      names, trademarks, service marks, or product names of the Licensor,
      except as required for reasonable and customary use in describing the
      origin of the Work and reproducing the content of the NOTICE file.

   7. Disclaimer of Warranty. Unless required by applicable law or
      agreed to in writing, Licensor provides the Work (and each
      Contributor provides its Contributions) on an "AS IS" BASIS,
      WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or
      implied, including, without limitation, any warranties or conditions
      of TITLE, NON-INFRINGEMENT, MERCHANTABILITY, or FITNESS FOR A
      PARTICULAR PURPOSE. You are solely responsible for determining the
      appropriateness of using or redistributing the Work and assume any
      risks associated with Your exercise of permissions under this License.

   8. Limitation of Liability. In no event and under no legal theory,
      whether in tort (including negligence), contract, or otherwise,
      unless required by applicable law (such as deliberate and grossly
      negligent acts) or agreed to in writing, shall any Contributor be
      liable to You for damages, including any direct, indirect, special,
      incidental, or consequential damages of any character arising as a
      result of this License or out of the use or inability to use the
      Work (including but not limited to damages for loss of goodwill,
      work stoppage, computer failure or malfunction, or any and all
      other commercial damages or losses), even if such Contributor
      has been advised of the possibility of such damages.

   9. Accepting Warranty or Additional Liability. While redistributing
      the Work or Derivative Works thereof, You may choose to offer,
      and charge a fee for, acceptance of support, warranty, indemnity,
      or other liability obligations and/or rights consistent with this
      License. However, in accepting such obligations, You may act only
      on Your own behalf and on Your sole responsibility, not on behalf
      of any other Contributor, and only if You agree to indemnify,
      defend, and hold each Contributor harmless for any liability
      incurred by, or claims asserted against, such Contributor by reason
      of your accepting any such warranty or additional liability.

   END OF TERMS AND CONDITIONS

   APPENDIX: How to apply the Apache License to your work.

      To apply the Apache License to your work, attach the following
      boilerplate notice, with the fields enclosed by brackets "[]"
      replaced with your own identifying information. (Don't include
      the brackets!)  The text should be enclosed in the appropriate
      comment syntax for the file format. We also recommend that a
      file or class name and description of purpose be included on the
      same "printed page" as the copyright notice for easier
      identification within third-party archives.

   Copyright [yyyy] [name of copyright owner]

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.

---

<p align="center">
  <strong>qdeeh ai</strong><br>
  Discover the Network. Understand the Service.<br><br>
  Built with Python 🐍
</p>
