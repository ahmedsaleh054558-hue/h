// name=main.dart
import 'dart:async';
import 'package:flutter/material.dart';

void main() {
  runApp(const QuranDashboardApp());
}

class QuranDashboardApp extends StatelessWidget {
  const QuranDashboardApp({super.key});

  @override
  Widget build(BuildContext context) {
    return Directionality( // تأكد من RTL
      textDirection: TextDirection.rtl,
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        title: 'لوحة متابعة الحلقة',
        theme: ThemeData(
          // ألوان هادئة ونقطة ذهبية
          primaryColor: const Color(0xFF1F7A6D),
          scaffoldBackgroundColor: const Color(0xFFF7FBF9),
          fontFamily: 'Tahoma',
          colorScheme: ColorScheme.fromSwatch().copyWith(secondary: const Color(0xFFC49A2E)),
        ),
        home: const DashboardPage(),
      ),
    );
  }
}

class DashboardPage extends StatefulWidget {
  const DashboardPage({super.key});

  @override
  State<DashboardPage> createState() => _DashboardPageState();
}

class _DashboardPageState extends State<DashboardPage> {
  // --- بيانات تجريبية مؤقتة (استبدلها لاحقًا ببيانات من API أو قاعدة بيانات) ---
  final String schoolName = 'مدرسة العادل لتعليم القرآن';
  final String circleName = 'حلقة الصحابي زيد بن حارثة رضي الله عنه';
  final String supervisor = 'اللجنة الفنية';

  // الأرقام هنا عرضية فقط — اجعلها قابلة للربط لاحقًا
  final int plannedTotal = 120;
  final int completedTotal = 92;

  // طلاب النموذج والشكل الدوري للتغيير كل 3 ثواني
  final List<StudentSummary> topStudents = [
    StudentSummary(name: 'إبراهيم خليل', percent: 86, indicators: ['الالتزام', 'حسن التجويد']),
    StudentSummary(name: 'أسامة محمد', percent: 74, indicators: ['تحسن ملحوظ', 'حفظ ثابت']),
    StudentSummary(name: 'حمزة وسيم', percent: 91, indicators: ['قوة الحفظ', 'تركيز ممتاز']),
  ];

  // قائمة الطلاب لعرض "إنجاز الطلاب"
  final List<StudentSummary> students = [
    StudentSummary(name: 'إبراهيم خليل', percent: 86),
    StudentSummary(name: 'أسامة محمد', percent: 74),
    StudentSummary(name: 'حمزة وسيم', percent: 91),
    StudentSummary(name: 'سلمى خالد', percent: 68),
    StudentSummary(name: 'فاطمة علي', percent: 79),
  ];

  int _currentTopIndex = 0;
  Timer? _rotationTimer;

  @override
  void initState() {
    super.initState();
    // تدوير اسماء الطلاب النموذجيين كل 3 ثواني
    if (topStudents.length > 1) {
      _rotationTimer = Timer.periodic(const Duration(seconds: 3), (_) {
        setState(() => _currentTopIndex = (_currentTopIndex + 1) % topStudents.length);
      });
    }
  }

  @override
  void dispose() {
    _rotationTimer?.cancel();
    super.dispose();
  }

  double get completionPercent =>
      plannedTotal == 0 ? 0 : (completedTotal / plannedTotal).clamp(0.0, 1.0);

  @override
  Widget build(BuildContext context) {
    final gold = const Color(0xFFC4992E);
    final accent = const Color(0xFF1F7A6D);

    return Scaffold(
      appBar: PreferredSize(
        preferredSize: const Size.fromHeight(120),
        child: SafeArea(
          child: Padding(
            padding: const EdgeInsets.symmetric(horizontal: 16, vertical: 12),
            child: _buildHeader(gold),
          ),
        ),
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.symmetric(horizontal: 16, vertical: 8),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            _buildPlanSummaryCard(accent, gold),
            const SizedBox(height: 16),
            _buildTopStudentCard(gold, accent),
            const SizedBox(height: 20),
            _buildStudentsSection(accent),
            const SizedBox(height: 40),
          ],
        ),
      ),
    );
  }

  Widget _buildHeader(Color gold) {
    return Row(
      crossAxisAlignment: CrossAxisAlignment.center,
      children: [
        // مساحة للشعار (مكان قابل للاستبدال لاحقًا بصورة فعلية)
        Container(
          width: 72,
          height: 72,
          decoration: BoxDecoration(
            color: Colors.white,
            borderRadius: BorderRadius.circular(12),
            boxShadow: const [BoxShadow(color: Colors.black12, blurRadius: 6, offset: Offset(0, 3))],
            border: Border.all(color: Colors.grey.shade200),
          ),
          child: Center(
            child: Icon(Icons.school, color: gold, size: 36),
          ),
        ),
        const SizedBox(width: 12),
        Expanded(
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text(
                schoolName,
                style: const TextStyle(fontSize: 16, fontWeight: FontWeight.w700, color: Colors.black87),
                maxLines: 1,
                overflow: TextOverflow.ellipsis,
              ),
              const SizedBox(height: 6),
              Text(
                circleName,
                style: const TextStyle(fontSize: 14, fontWeight: FontWeight.w600, color: Color(0xFF2E5160)),
                maxLines: 2,
                overflow: TextOverflow.ellipsis,
              ),
              const SizedBox(height: 6),
              Text(
                'الجهة المشرفة: $supervisor',
                style: const TextStyle(fontSize: 12, color: Colors.black54),
              ),
            ],
          ),
        ),
        // زر بسيط للخيارات أو الربط المستقبلي
        IconButton(
          onPressed: () {
            // ربط لاحق: صفحة إعدادات الحلقة أو رفع شعار
          },
          icon: Icon(Icons.more_vert, color: Colors.grey.shade700),
        ),
      ],
    );
  }

  Widget _buildPlanSummaryCard(Color accent, Color gold) {
    final remaining = (plannedTotal - completedTotal).clamp(0, plannedTotal);
    final percent = (completionPercent * 100).round();

    return Card(
      elevation: 4,
      shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(14)),
      child: Padding(
        padding: const EdgeInsets.all(14),
        child: Row(
          children: [
            // دائرة النسبة
            SizedBox(
              width: 110,
              height: 110,
              child: Stack(
                alignment: Alignment.center,
                children: [
                  SizedBox(
                    width: 110,
                    height: 110,
                    child: CircularProgressIndicator(
                      value: completionPercent,
                      strokeWidth: 10,
                      backgroundColor: Colors.grey.shade200,
                      valueColor: AlwaysStoppedAnimation<Color>(gold),
                    ),
                  ),
                  Column(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      Text(
                        '$percent%',
                        style: const TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
                      ),
                      const SizedBox(height: 4),
                      const Text('نسبة الإنجاز', style: TextStyle(fontSize: 12, color: Colors.black54)),
                    ],
                  ),
                ],
              ),
            ),
            const SizedBox(width: 14),
            // الأرقام الأساسية
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  _statRow('إجمالي المخطط', plannedTotal.toString(), accent),
                  const SizedBox(height: 8),
                  _statRow('إجمالي المنفّذ', completedTotal.toString(), Colors.green.shade700),
                  const SizedBox(height: 8),
                  _statRow('المتبقي', remaining.toString(), Colors.red.shade700),
                  const SizedBox(height: 12),
                  Text(
                    'اضغط على أي رقم لربطه بملف المتابعة لاحقًا',
                    style: TextStyle(fontSize: 11, color: Colors.grey.shade600),
                  ),
                ],
              ),
            )
          ],
        ),
      ),
    );
  }

  Widget _statRow(String label, String value, Color valueColor) {
    return Row(
      children: [
        Expanded(child: Text(label, style: const TextStyle(fontSize: 14, color: Colors.black87))),
        GestureDetector(
          onTap: () {
            // ربط لاحق: التنقل إلى ملف المتابعة أو فتح فلتر معين
          },
          child: Container(
            padding: const EdgeInsets.symmetric(horizontal: 10, vertical: 6),
            decoration: BoxDecoration(
              color: valueColor.withOpacity(0.08),
              borderRadius: BorderRadius.circular(8),
            ),
            child: Text(value, style: TextStyle(fontWeight: FontWeight.w700, color: valueColor)),
          ),
        )
      ],
    );
  }

  Widget _buildTopStudentCard(Color gold, Color accent) {
    final StudentSummary current = topStudents[_currentTopIndex];

    return Card(
      shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(14)),
      elevation: 3,
      child: Padding(
        padding: const EdgeInsets.all(14),
        child: Row(
          children: [
            // أيقونة أو صورة الطالب
            Container(
              width: 74,
              height: 74,
              decoration: BoxDecoration(
                color: Colors.white,
                borderRadius: BorderRadius.circular(12),
                border: Border.all(color: Colors.grey.shade200),
                boxShadow: const [BoxShadow(color: Colors.black12, blurRadius: 6, offset: Offset(0, 3))],
              ),
              child: Center(child: Icon(Icons.emoji_events, color: gold, size: 36)),
            ),
            const SizedBox(width: 12),
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  const Text('الطالب النموذجي', style: TextStyle(fontSize: 13, color: Colors.black54)),
                  const SizedBox(height: 8),
                  AnimatedSwitcher(
                    duration: const Duration(milliseconds: 600),
                    transitionBuilder: (child, anim) => FadeTransition(opacity: anim, child: child),
                    child: Text(
                      current.name,
                      key: ValueKey(current.name),
                      style: const TextStyle(fontSize: 16, fontWeight: FontWeight.w800),
                    ),
                  ),
                  const SizedBox(height: 8),
                  Row(
                    children: [
                      // نسبة الطالب
                      Container(
                        padding: const EdgeInsets.symmetric(horizontal: 10, vertical: 6),
                        decoration: BoxDecoration(
                          color: Colors.grey.shade100,
                          borderRadius: BorderRadius.circular(8),
                        ),
                        child: Text('${current.percent}%', style: TextStyle(fontWeight: FontWeight.w700, color: accent)),
                      ),
                      const SizedBox(width: 12),
                      // مؤشرات التقييم الأساسية
                      Expanded(
                        child: Wrap(
                          spacing: 8,
                          runSpacing: 6,
                          children: current.indicators.map((ind) {
                            return Chip(
                              backgroundColor: Colors.white,
                              label: Text(ind, style: const TextStyle(fontSize: 12)),
                              avatar: const Icon(Icons.check_circle, size: 16, color: Colors.green),
                              elevation: 1,
                            );
                          }).toList(),
                        ),
                      ),
                    ],
                  ),
                ],
              ),
            ),
            IconButton(
              onPressed: () {
                // ربط لاحق: فتح ملف الطالب إذا احتجت لاحقًا (ممنوع الآن per request)
              },
              icon: Icon(Icons.arrow_forward_ios, color: Colors.grey.shade600, size: 18),
            )
          ],
        ),
      ),
    );
  }

  Widget _buildStudentsSection(Color accent) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text('إنجاز الطلاب', style: TextStyle(fontSize: 16, fontWeight: FontWeight.w700, color: accent)),
        const SizedBox(height: 10),
        Card(
          elevation: 2,
          shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(12)),
          child: Padding(
            padding: const EdgeInsets.symmetric(vertical: 10, horizontal: 8),
            child: Column(
              children: students.map((s) => _buildStudentRow(s)).toList(),
            ),
          ),
        )
      ],
    );
  }

  Widget _buildStudentRow(StudentSummary s) {
    return Padding(
      padding: const EdgeInsets.symmetric(vertical: 8, horizontal: 6),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.stretch,
        children: [
          Row(
            children: [
              Expanded(
                  child: Text(
                s.name,
                style: const TextStyle(fontWeight: FontWeight.w600),
              )),
              Text('${s.percent}%', style: const TextStyle(fontWeight: FontWeight.w700)),
            ],
          ),
          const SizedBox(height: 6),
          ClipRRect(
            borderRadius: BorderRadius.circular(8),
            child: LinearProgressIndicator(
              value: (s.percent / 100).clamp(0.0, 1.0),
              minHeight: 10,
              backgroundColor: Colors.grey.shade200,
              valueColor: AlwaysStoppedAnimation<Color>(_progressColor(s.percent)),
            ),
          ),
        ],
      ),
    );
  }

  Color _progressColor(int percent) {
    if (percent >= 85) return const Color(0xFF1F7A6D); // أخضر هادئ
    if (percent >= 70) return const Color(0xFF2E7D32); // أخضر متوسط
    if (percent >= 50) return const Color(0xFFED9C2A); // ذهبي / برتقالي
    return const Color(0xFFB00020); // أحمر
  }
}

class StudentSummary {
  final String name;
  final int percent;
  final List<String> indicators;
  StudentSummary({required this.name, required this.percent, this.indicators = const []});
}
