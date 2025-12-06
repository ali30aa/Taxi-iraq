# تصور مبدئي لواجهات برمجة التطبيقات (Backend API)

هذه مسودة أولية يمكن تطويرها لاحقاً مع تفاصيل الأمان والقيود، وتفترض بوابة REST JSON.

## 1. التوثيق والجلسات
- **POST /v1/auth/request-otp**: طلب رمز OTP عبر SMS/WhatsApp. المدخل: `phone`، `channel`.
- **POST /v1/auth/verify-otp**: التحقق وإصدار توكن JWT قصير + refresh token.
- **POST /v1/auth/refresh**: تجديد التوكن.
- **POST /v1/auth/logout**: إلغاء الجلسة.

## 2. الحساب والمستخدم
- **GET /v1/me**: معلومات الحساب واللغة المفضلة والمركبة.
- **PATCH /v1/me**: تحديث الاسم، اللغة، نوع المركبة، المنزل/العمل.

## 3. المفضلة والوجهات
- **GET /v1/favorites**: قائمة الوجهات المحفوظة.
- **POST /v1/favorites**: إضافة وجهة.
- **PATCH /v1/favorites/{id}**: تعديل اسم/ملاحظات/موقع الوجهة.
- **DELETE /v1/favorites/{id}**: حذف.

## 4. البحث والخرائط
- **GET /v1/search**: geocoding + POI مع معلمات `q`, `lat`, `lng`, `radius`.
- **GET /v1/tiles/{z}/{x}/{y}.mvt**: بلاطات Vector Tiles (مع تحكم صلاحيات التنزيل).

## 5. الرحلات والتوجيه
- **POST /v1/routes**: حساب مسار (origin, destination, waypoints[]) مع خيارات `mode` و`avoid` و`prefer`.
- **POST /v1/trips**: إنشاء رحلة حيّة مع المسار الأولي.
- **PATCH /v1/trips/{id}**: تحديث التقدم، إعادة التوجيه، أو تعديل الوجهات.
- **POST /v1/trips/{id}/share**: إنشاء رابط مشاركة حيّة (ينتهي بعد مدة محددة).

## 6. التنبيهات المجتمعية
- **POST /v1/alerts**: إنشاء تنبيه (نوع، موقع، نصف قطر، شدة، مدة صلاحية).
- **PATCH /v1/alerts/{id}**: تحديث الحالة أو الإبطال.
- **POST /v1/alerts/{id}/vote**: تصويت (confirm/deny) لرفع موثوقية التنبيه.
- **GET /v1/alerts/nearby**: جلب التنبيهات ضمن نصف قطر مع تصفية حسب النوع.

## 7. البيانات في الزمن الحقيقي
- **WebSocket /v1/ws/traffic**: بث تحديثات حركة المرور والبلاغات المؤكدة.
- **WebSocket /v1/ws/trips/{id}**: بث رحلة حيّة للمشاركِين.

## 8. المراقبة والإدارة
- **GET /v1/admin/ingest/status**: حالة استيراد بيانات OSM/تعديلات المجتمع.
- **POST /v1/admin/alerts/{id}/review**: قبول/رفض تنبيه بعد المراجعة اليدوية.
- **GET /v1/admin/metrics**: مؤشرات الأداء واستهلاك الخدمة.

## 9. مبادئ الأمان
- جميع نقاط النهاية خلف TLS مع JWT قصير العمر + Refresh token.
- صلاحيات Role-Based (user/moderator/admin) للتنبيهات والإشراف.
- معدل طلبات (rate limiting) خاصة لنقاط OTP والبحث.
