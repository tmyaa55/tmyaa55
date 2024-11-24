from flask import Flask, request, jsonify, render_template
from fpdf import FPDF

app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/submit", methods=["POST"])
def submit():
    data = request.json

    # Extract data from the request
    title = data.get("title", "غير متوفر")
    introduction = data.get("introduction", "غير متوفر")
    description = data.get("description", "غير متوفر")
    risks = data.get("risks", "غير متوفر")
    action = data.get("action", "غير متوفر")

    # Structure the report
    report = f"""
    **عنوان الملاحظة:** {title}

    **مقدمة:** 
    {introduction}

    **وصف الملاحظات:** 
    {description}

    **المخاطر المحتملة:** 
    {risks}

    **الإجراء التصحيحي الموصى به:** 
    {action}
    """

    # Generate PDF with Arabic support
    pdf = FPDF()
    pdf.add_page()
    pdf.add_font('Arial', '', fname='Arial.ttf', uni=True)  # Add Arabic font
    pdf.set_font("Arial", size=12)
    pdf.set_auto_page_break(auto=True, margin=15)
    pdf.multi_cell(0, 10, txt=report, align="R")
    pdf_file = "audit_report_ar.pdf"
    pdf.output(pdf_file)

    return jsonify({"message": "تم إنشاء التقرير بنجاح!", "pdf": pdf_file})

if __name__ == "__main__":
    app.run(debug=True)
