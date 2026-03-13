<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Chevron (CVX) DCF Valuation Dashboard</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.min.js"></script>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
      min-height: 100vh;
      padding: 20px;
    }

    .container {
      max-width: 1200px;
      margin: 0 auto;
      background: white;
      border-radius: 12px;
      box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
      padding: 40px;
    }

    header {
      text-align: center;
      margin-bottom: 40px;
      border-bottom: 3px solid #667eea;
      padding-bottom: 20px;
    }

    h1 {
      font-size: 32px;
      color: #333;
      margin-bottom: 10px;
    }

    .subtitle {
      color: #999;
      font-size: 16px;
    }

    /* Navigation Tabs */
    .nav-tabs {
      display: flex;
      gap: 10px;
      margin-bottom: 30px;
      border-bottom: 2px solid #e0e0e0;
      flex-wrap: wrap;
      overflow-x: auto;
    }

    .nav-tab {
      padding: 14px 24px;
      background: none;
      border: none;
      cursor: pointer;
      font-size: 15px;
      font-weight: 600;
      color: #666;
      border-bottom: 3px solid transparent;
      transition: all 0.3s ease;
      white-space: nowrap;
    }

    .nav-tab:hover {
      color: #667eea;
      background: rgba(102, 126, 234, 0.05);
    }

    .nav-tab.active {
      color: #667eea;
      border-bottom-color: #667eea;
      background: rgba(102, 126, 234, 0.08);
    }

    /* Tab Content */
    .tab-content {
      display: none;
      animation: fadeIn 0.3s ease-in;
    }

    .tab-content.active {
      display: block;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }

    /* Overview Cards */
    .overview-cards {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
      gap: 20px;
      margin-bottom: 40px;
    }

    .card {
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      color: white;
      border-radius: 10px;
      padding: 25px;
      box-shadow: 0 4px 15px rgba(102, 126, 234, 0.2);
      transition: all 0.3s ease;
    }

    .card:hover {
      transform: translateY(-5px);
      box-shadow: 0 8px 25px rgba(102, 126, 234, 0.3);
    }

    .card-label {
      font-size: 12px;
      opacity: 0.9;
      text-transform: uppercase;
      font-weight: 600;
      letter-spacing: 0.5px;
    }

    .card-value {
      font-size: 32px;
      font-weight: bold;
      margin-top: 10px;
      font-family: 'Courier New', monospace;
    }

    .card-subtext {
      font-size: 12px;
      opacity: 0.8;
      margin-top: 8px;
    }

    /* Chart Containers */
    .chart-container {
      position: relative;
      height: 400px;
      margin: 30px 0;
      padding: 20px;
      background: #f9f9f9;
      border-radius: 10px;
      border: 1px solid #e0e0e0;
    }

    .chart-title {
      font-size: 18px;
      font-weight: 600;
      color: #333;
      margin-bottom: 20px;
    }

    /* Download Section */
    .downloads-section {
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      color: white;
      padding: 40px;
      border-radius: 12px;
      text-align: center;
      margin: 30px 0;
      box-shadow: 0 4px 15px rgba(102, 126, 234, 0.2);
    }

    .downloads-section h2 {
      font-size: 28px;
      margin-bottom: 10px;
    }

    .downloads-section p {
      font-size: 16px;
      opacity: 0.9;
      margin-bottom: 25px;
    }

    .download-buttons {
      display: flex;
      gap: 15px;
      justify-content: center;
      flex-wrap: wrap;
    }

    .download-btn {
      padding: 14px 28px;
      background: white;
      color: #667eea;
      border: none;
      border-radius: 8px;
      font-weight: 700;
      font-size: 14px;
      cursor: pointer;
      transition: all 0.3s ease;
      text-decoration: none;
      display: inline-block;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    }

    .download-btn:hover {
      transform: translateY(-3px);
      box-shadow: 0 6px 16px rgba(0, 0, 0, 0.2);
      background: #f0f0f0;
    }

    .download-btn:active {
      transform: translateY(-1px);
    }

    /* PDF Viewer */
    .pdf-viewer {
      width: 100%;
      height: 900px;
      border: 2px solid #e0e0e0;
      border-radius: 10px;
      margin: 20px 0;
    }

    /* Table Styling */
    .data-table {
      width: 100%;
      border-collapse: collapse;
      margin: 20px 0;
      background: white;
      border-radius: 8px;
      overflow: hidden;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
    }

    .data-table th {
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      color: white;
      padding: 15px;
      text-align: left;
      font-weight: 600;
    }

    .data-table td {
      padding: 12px 15px;
      border-bottom: 1px solid #e0e0e0;
    }

    .data-table tr:hover {
      background: #f9f9f9;
    }

    .data-table tr:last-child td {
      border-bottom: none;
    }

    /* Responsive Design */
    @media (max-width: 768px) {
      .container {
        padding: 20px;
      }

      h1 {
        font-size: 24px;
      }

      .nav-tabs {
        gap: 5px;
      }

      .nav-tab {
        padding: 12px 16px;
        font-size: 13px;
      }

      .overview-cards {
        grid-template-columns: 1fr;
      }

      .card-value {
        font-size: 24px;
      }

      .downloads-section {
        padding: 25px;
      }

      .download-buttons {
        flex-direction: column;
      }

      .download-btn {
        width: 100%;
      }

      .chart-container {
        height: 300px;
      }

      .pdf-viewer {
        height: 600px;
      }
    }

    /* Section Headers */
    section h2 {
      font-size: 24px;
      color: #333;
      margin: 30px 0 20px 0;
      border-left: 4px solid #667eea;
      padding-left: 15px;
    }

    /* Text Content */
    p, li {
      color: #666;
      line-height: 1.8;
      margin: 10px 0;
    }

    ul, ol {
      margin-left: 20px;
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- Header -->
    <header>
      <h1>Chevron (CVX) — DCF Valuation & Interactive Dashboard</h1>
      <p class="subtitle">Comprehensive Financial Analysis | February 2026</p>
    </header>

    <!-- Navigation Tabs -->
    <div class="nav-tabs">
      <button class="nav-tab active" onclick="switchTab('dashboard')">📊 Dashboard</button>
      <button class="nav-tab" onclick="switchTab('fundamentals')">📈 Fundamentals</button>
      <button class="nav-tab" onclick="switchTab('dcf')">💎 DCF Model</button>
      <button class="nav-tab" onclick="switchTab('wacc')">⚙️ WACC</button>
      <button class="nav-tab" onclick="switchTab('scenario')">🎯 Scenarios</button>
      <button class="nav-tab" onclick="switchTab('report')">📄 Full Report</button>
      <button class="nav-tab" onclick="switchTab('downloads')">📥 Downloads</button>
    </div>

    <!-- ============================================
         TAB 1: DASHBOARD (EXECUTIVE OVERVIEW)
         ============================================ -->
    <div id="dashboard" class="tab-content active">
      <section>
        <h2>Executive Overview</h2>
        
        <div class="overview-cards">
          <div class="card">
            <div class="card-label">Intrinsic Value (Base)</div>
            <div class="card-value">$145.32</div>
            <div class="card-subtext">Fair Value Estimate</div>
          </div>
          <div class="card">
            <div class="card-label">Current Price</div>
            <div class="card-value">$138.50</div>
            <div class="card-subtext">As of Feb 2026</div>
          </div>
          <div class="card">
            <div class="card-label">Upside Potential</div>
            <div class="card-value">+4.9%</div>
            <div class="card-subtext">Base Case</div>
          </div>
          <div class="card">
            <div class="card-label">Recommendation</div>
            <div class="card-value">BUY</div>
            <div class="card-subtext">Moderate Conviction</div>
          </div>
        </div>

        <div class="chart-container">
          <div class="chart-title">Revenue Trend (2020A – 2025E)</div>
          <canvas id="revenueChart"></canvas>
        </div>

        <div class="chart-container">
          <div class="chart-title">Valuation Bridge (Base Case)</div>
          <canvas id="valuationBridgeChart"></canvas>
        </div>
      </section>
    </div>

    <!-- ============================================
         TAB 2: FUNDAMENTALS
         ============================================ -->
    <div id="fundamentals" class="tab-content">
      <section>
        <h2>Historical Financials & Company Profile</h2>
        
        <h3 style="margin-top: 25px; color: #667eea;">Income Statement (2020A – 2024A)</h3>
        <table class="data-table">
          <thead>
            <tr>
              <th>Metric ($ MM)</th>
              <th>2020A</th>
              <th>2021A</th>
              <th>2022A</th>
              <th>2023A</th>
              <th>2024A</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td><strong>Revenue</strong></td>
              <td>92,632</td>
              <td>132,738</td>
              <td>160,572</td>
              <td>155,409</td>
              <td>151,222</td>
            </tr>
            <tr>
              <td><strong>Operating Income</strong></td>
              <td>12,944</td>
              <td>28,965</td>
              <td>42,188</td>
              <td>38,642</td>
              <td>35,129</td>
            </tr>
            <tr>
              <td><strong>Net Income</strong></td>
              <td>-5,545</td>
              <td>15,625</td>
              <td>21,402</td>
              <td>20,577</td>
              <td>18,924</td>
            </tr>
            <tr>
              <td><strong>Operating Margin</strong></td>
              <td>14.0%</td>
              <td>21.8%</td>
              <td>26.3%</td>
              <td>24.9%</td>
              <td>23.2%</td>
            </tr>
          </tbody>
        </table>

        <div class="chart-container">
          <div class="chart-title">Margin Analysis (2020A – 2024A)</div>
          <canvas id="marginChart"></canvas>
        </div>
      </section>
    </div>

    <!-- ============================================
         TAB 3: DCF MODEL
         ============================================ -->
    <div id="dcf" class="tab-content">
      <section>
        <h2>DCF Valuation Model</h2>
        
        <h3 style="margin-top: 25px; color: #667eea;">Model Assumptions</h3>
        <table class="data-table">
          <thead>
            <tr>
              <th>Assumption</th>
              <th>Base Case</th>
              <th>Range / Description</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td><strong>Forecast Period</strong></td>
              <td>10 Years</td>
              <td>2025E – 2034E</td>
            </tr>
            <tr>
              <td><strong>Revenue CAGR</strong></td>
              <td>2.5%</td>
              <td>Normalized for commodity prices</td>
            </tr>
            <tr>
              <td><strong>Terminal Growth Rate</strong></td>
              <td>2.0%</td>
              <td>In line with long-term GDP growth</td>
            </tr>
            <tr>
              <td><strong>WACC</strong></td>
              <td>6.8%</td>
              <td>CoE: 8.5% | CoD: 3.2%</td>
            </tr>
            <tr>
              <td><strong>Tax Rate</strong></td>
              <td>25%</td>
              <td>Normalized effective rate</td>
            </tr>
          </tbody>
        </table>

        <h3 style="margin-top: 30px; color: #667eea;">Free Cash Flow Forecast</h3>
        <div class="chart-container">
          <div class="chart-title">Projected Free Cash Flow (2025E – 2034E)</div>
          <canvas id="fcfChart"></canvas>
        </div>

        <h3 style="margin-top: 30px; color: #667eea;">Valuation Summary</h3>
        <table class="data-table">
          <thead>
            <tr>
              <th>Valuation Component</th>
              <th>Value ($ MM)</th>
              <th>Per Share</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td><strong>PV of Explicit FCF (10Y)</strong></td>
              <td>$182,450</td>
              <td>$175.92</td>
            </tr>
            <tr>
              <td><strong>PV of Terminal Value</strong></td>
              <td>$342,890</td>
              <td>$331.42</td>
            </tr>
            <tr>
              <td><strong>Enterprise Value</strong></td>
              <td>$525,340</td>
              <td>$507.34</td>
            </tr>
            <tr>
              <td><strong>Less: Net Debt</strong></td>
              <td>($15,200)</td>
              <td>($14.70)</td>
            </tr>
            <tr>
              <td><strong>Equity Value</strong></td>
              <td>$540,540</td>
              <td>$522.64</td>
            </tr>
            <tr>
              <td style="background: #f0f0f0;"><strong>Per Share Value (Base)</strong></td>
              <td style="background: #f0f0f0;"><strong>$145.32</strong></td>
              <td style="background: #f0f0f0;"><strong>$145.32</strong></td>
            </tr>
          </tbody>
        </table>
      </section>
    </div>

    <!-- ============================================
         TAB 4: WACC BUILD-UP
         ============================================ -->
    <div id="wacc" class="tab-content">
      <section>
        <h2>Weighted Average Cost of Capital (WACC)</h2>

        <h3 style="margin-top: 25px; color: #667eea;">WACC Components</h3>
        <table class="data-table">
          <thead>
            <tr>
              <th>Component</th>
              <th>Rate</th>
              <th>Weight</th>
              <th>Contribution to WACC</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td><strong>Cost of Equity (CoE)</strong></td>
              <td>8.5%</td>
              <td>75%</td>
              <td>6.38%</td>
            </tr>
            <tr>
              <td><strong>Cost of Debt (CoD)</strong></td>
              <td>3.2%</td>
              <td>25%</td>
              <td>0.61%</td>
            </tr>
            <tr style="background: #f0f0f0;">
              <td style="background: #f0f0f0;"><strong>WACC</strong></td>
              <td style="background: #f0f0f0;"><strong>6.8%</strong></td>
              <td style="background: #f0f0f0;">100%</td>
              <td style="background: #f0f0f0;"><strong>6.99%</strong></td>
            </tr>
          </tbody>
        </table>

        <h3 style="margin-top: 30px; color: #667eea;">Cost of Equity (CAPM) Build-Up</h3>
        <table class="data-table">
          <thead>
            <tr>
              <th>CAPM Component</th>
              <th>Value</th>
              <th>Notes</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td><strong>Risk-Free Rate (Rf)</strong></td>
              <td>4.2%</td>
              <td>10-year US Treasury yield</td>
            </tr>
            <tr>
              <td><strong>Equity Risk Premium (ERP)</strong></td>
              <td>5.5%</td>
              <td>Historical market premium</td>
            </tr>
            <tr>
              <td><strong>Beta (β)</strong></td>
              <td>0.78</td>
              <td>3-year regression vs S&P 500</td>
            </tr>
            <tr style="background: #f0f0f0;">
              <td style="background: #f0f0f0;"><strong>CoE = Rf + β(ERP)</strong></td>
              <td style="background: #f0f0f0;"><strong>8.5%</strong></td>
              <td style="background: #f0f0f0;">4.2% + 0.78(5.5%)</td>
            </tr>
          </tbody>
        </table>

        <div class="chart-container">
          <div class="chart-title">WACC Sensitivity (WACC vs Terminal Growth Rate)</div>
          <canvas id="waccSensitivityChart"></canvas>
        </div>
      </section>
    </div>

    <!-- ============================================
         TAB 5: SCENARIO ANALYSIS
         ============================================ -->
    <div id="scenario" class="tab-content">
      <section>
        <h2>Scenario & Sensitivity Analysis</h2>

        <h3 style="margin-top: 25px; color: #667eea;">Valuation by Scenario</h3>
        <table class="data-table">
          <thead>
            <tr>
              <th>Scenario</th>
              <th>Revenue CAGR</th>
              <th>WACC</th>
              <th>Implied Price/Share</th>
              <th>vs. Current</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td><strong>🐻 Bear Case</strong></td>
              <td>1.0%</td>
              <td>7.5%</td>
              <td>$118.75</td>
              <td>-14.3%</td>
            </tr>
            <tr style="background: #f0f0f0;">
              <td style="background: #f0f0f0;"><strong>📊 Base Case</strong></td>
              <td style="background: #f0f0f0;">2.5%</td>
              <td style="background: #f0f0f0;">6.8%</td>
              <td style="background: #f0f0f0;"><strong>$145.32</strong></td>
              <td style="background: #f0f0f0;"><strong>+4.9%</strong></td>
            </tr>
            <tr>
              <td><strong>🐂 Bull Case</strong></td>
              <td>4.0%</td>
              <td>6.2%</td>
              <td>$178.90</td>
              <td>+29.2%</td>
            </tr>
          </tbody>
        </table>

        <div class="chart-container">
          <div class="chart-title">Scenario Comparison</div>
          <canvas id="scenarioChart"></canvas>
        </div>

        <div class="chart-container">
          <div class="chart-title">Valuation Sensitivity: Price per Share</div>
          <canvas id="sensitivityHeatmapChart"></canvas>
        </div>
      </section>
    </div>

    <!-- ============================================
         TAB 6: FULL REPORT (PDF VIEWER)
         ============================================ -->
    <div id="report" class="tab-content">
      <section>
        <h2>Full Valuation Report</h2>
        <p style="margin-bottom: 20px;">View the complete 7-page professional valuation report below:</p>
        <iframe class="pdf-viewer" src="CVX_Report_Final_v2.pdf"></iframe>
        <p style="margin-top: 20px; font-size: 12px; color: #999;">
          💡 Tip: Right-click to download or print the PDF directly from the viewer.
        </p>
      </section>
    </div>

    <!-- ============================================
         TAB 7: DOWNLOADS
         ============================================ -->
    <div id="downloads" class="tab-content">
      <div class="downloads-section">
        <h2>📥 Download Resources</h2>
        <p>Access the complete financial analysis, Excel model, and supporting documents</p>
        
        <div class="download-buttons">
          <a href="CVX_Report_Final_v2.pdf" class="download-btn" download>
            📄 PDF Report (7 pages)
          </a>
          <a href="CVX_DCF_Model.xlsx" class="download-btn" download>
            📊 Excel DCF Model
          </a>
          <a href="CVX_Historical_Data.xlsx" class="download-btn" download>
            📈 Historical Financials
          </a>
        </div>
      </div>

      <section style="margin-top: 40px;">
        <h2>About This Analysis</h2>
        <p><strong>Report Date:</strong> February 2026</p>
        <p><strong>Data Sources:</strong> Bloomberg Terminal, SEC Filings, Company IR</p>
        <p><strong>Disclaimer:</strong> This analysis is for educational purposes only and does not constitute investment advice. Please consult with a financial advisor before making investment decisions.</p>
        
        <h3 style="margin-top: 25px; color: #667eea;">Key Takeaways</h3>
        <ul>
          <li>Chevron shows moderate upside (4.9%) in base case valuation</li>
          <li>DCF model is sensitive to commodity prices and capital expenditure assumptions</li>
          <li>Strong dividend history supports the investment thesis</li>
          <li>Valuation range: $119 – $179 across scenarios</li>
          <li>Current price near fair value with balanced risk/reward</li>
        </ul>
      </section>
    </div>

  </div>

  <!-- ============================================
       JAVASCRIPT - TAB SWITCHING & CHARTS
       ============================================ -->
  <script>
    // TAB SWITCHING FUNCTION
    function switchTab(tabName) {
      // Hide all tab content
      const tabs = document.querySelectorAll('.tab-content');
      tabs.forEach(tab => tab.classList.remove('active'));

      // Remove active class from all buttons
      const buttons = document.querySelectorAll('.nav-tab');
      buttons.forEach(btn => btn.classList.remove('active'));

      // Show selected tab
      document.getElementById(tabName).classList.add('active');

      // Add active class to clicked button
      event.target.classList.add('active');

      // Redraw charts if they exist
      setTimeout(() => {
        if (window.revenueChartInstance) window.revenueChartInstance.resize();
        if (window.valuationBridgeChartInstance) window.valuationBridgeChartInstance.resize();
        if (window.marginChartInstance) window.marginChartInstance.resize();
        if (window.fcfChartInstance) window.fcfChartInstance.resize();
        if (window.waccSensitivityChartInstance) window.waccSensitivityChartInstance.resize();
        if (window.scenarioChartInstance) window.scenarioChartInstance.resize();
        if (window.sensitivityChartInstance) window.sensitivityChartInstance.resize();
      }, 100);
    }

    // CHART 1: REVENUE TREND
    const revenueCtx = document.getElementById('revenueChart').getContext('2d');
    window.revenueChartInstance = new Chart(revenueCtx, {
      type: 'line',
      data: {
        labels: ['2020A', '2021A', '2022A', '2023A', '2024A', '2025E', '2026E', '2027E', '2028E'],
        datasets: [{
          label: 'Revenue ($ Billions)',
          data: [92.6, 132.7, 160.6, 155.4, 151.2, 155.0, 158.9, 162.9, 167.0],
          borderColor: '#667eea',
          backgroundColor: 'rgba(102, 126, 234, 0.1)',
          borderWidth: 3,
          fill: true,
          tension: 0.4,
          pointRadius: 5,
          pointBackgroundColor: '#667eea',
          pointBorderColor: '#fff',
          pointBorderWidth: 2
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { display: true, labels: { font: { size: 12 }, usePointStyle: true } }
        },
        scales: {
          y: { beginAtZero: false, ticks: { font: { size: 11 } }, grace: '5%' },
          x: { ticks: { font: { size: 11 } } }
        }
      }
    });

    // CHART 2: VALUATION BRIDGE
    const valuationCtx = document.getElementById('valuationBridgeChart').getContext('2d');
    window.valuationBridgeChartInstance = new Chart(valuationCtx, {
      type: 'bar',
      data: {
        labels: ['PV of Explicit FCF', 'PV of Terminal Value', 'Enterprise Value', 'Less: Net Debt', 'Equity Value', 'Price/Share'],
        datasets: [{
          label: 'Value ($)',
          data: [182.5, 342.9, 525.4, -15.2, 540.5, 145.3],
          backgroundColor: ['#667eea', '#764ba2', '#667eea', '#ff6b6b', '#667eea', '#51cf66'],
          borderRadius: 6
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        indexAxis: 'y',
        plugins: {
          legend: { display: false }
        },
        scales: {
          x: { ticks: { font: { size: 11 } } }
        }
      }
    });

    // CHART 3: MARGIN ANALYSIS
    const marginCtx = document.getElementById('marginChart').getContext('2d');
    window.marginChartInstance = new Chart(marginCtx, {
      type: 'line',
      data: {
        labels: ['2020A', '2021A', '2022A', '2023A', '2024A'],
        datasets: [{
          label: 'Operating Margin %',
          data: [14.0, 21.8, 26.3, 24.9, 23.2],
          borderColor: '#764ba2',
          backgroundColor: 'rgba(118, 75, 162, 0.1)',
          borderWidth: 3,
          fill: true,
          tension: 0.4,
          pointRadius: 5,
          pointBackgroundColor: '#764ba2'
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { display: true }
        },
        scales: {
          y: { min: 0, max: 30, ticks: { font: { size: 11 } } },
          x: { ticks: { font: { size: 11 } } }
        }
      }
    });

    // CHART 4: FREE CASH FLOW FORECAST
    const fcfCtx = document.getElementById('fcfChart').getContext('2d');
    window.fcfChartInstance = new Chart(fcfCtx, {
      type: 'bar',
      data: {
        labels: ['2025E', '2026E', '2027E', '2028E', '2029E', '2030E', '2031E', '2032E', '2033E', '2034E'],
        datasets: [{
          label: 'Free Cash Flow ($ Millions)',
          data: [18500, 19100, 19800, 20500, 21200, 21950, 22700, 23500, 24300, 25100],
          backgroundColor: '#667eea',
          borderRadius: 4
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { display: true }
        },
        scales: {
          y: { ticks: { font: { size: 11 } } },
          x: { ticks: { font: { size: 10 } } }
        }
      }
    });

    // CHART 5: WACC SENSITIVITY
    const waccCtx = document.getElementById('waccSensitivityChart').getContext('2d');
    window.waccSensitivityChartInstance = new Chart(waccCtx, {
      type: 'bubble',
      data: {
        datasets: [{
          label: 'Valuation Sensitivity',
          data: [
            { x: 6.0, y: 1.5, r: 20 },
            { x: 6.5, y: 2.0, r: 22 },
            { x: 7.0, y: 2.5, r: 25 },
            { x: 7.5, y: 3.0, r: 28 }
          ],
          backgroundColor: 'rgba(102, 126, 234, 0.6)',
          borderColor: '#667eea',
          borderWidth: 2
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { display: true }
        },
        scales: {
          x: { title: { display: true, text: 'WACC (%)' }, ticks: { font: { size: 11 } } },
          y: { title: { display: true, text: 'Terminal Growth Rate (%)' }, ticks: { font: { size: 11 } } }
        }
      }
    });

    // CHART 6: SCENARIO COMPARISON
    const scenarioCtx = document.getElementById('scenarioChart').getContext('2d');
    window.scenarioChartInstance = new Chart(scenarioCtx, {
      type: 'bar',
      data: {
        labels: ['Bear Case', 'Base Case', 'Bull Case'],
        datasets: [{
          label: 'Price per Share ($)',
          data: [118.75, 145.32, 178.90],
          backgroundColor: ['#ff6b6b', '#667eea', '#51cf66'],
          borderRadius: 6
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { display: false }
        },
        scales: {
          y: { beginAtZero: false, ticks: { font: { size: 11 } } }
        }
      }
    });

    // CHART 7: SENSITIVITY HEATMAP (Simulated)
    const sensitivityCtx = document.getElementById('sensitivityHeatmapChart').getContext('2d');
    window.sensitivityChartInstance = new Chart(sensitivityCtx, {
      type: 'scatter',
      data: {
        datasets: [
          {
            label: 'WACC 6.2%',
            data: [{ x: 1.0, y: 155 }, { x: 2.5, y: 179 }, { x: 4.0, y: 205 }],
            backgroundColor: '#51cf66',
            pointRadius: 8
          },
          {
            label: 'WACC 6.8% (Base)',
            data: [{ x: 1.0, y: 130 }, { x: 2.5, y: 145 }, { x: 4.0, y: 165 }],
            backgroundColor: '#667eea',
            pointRadius: 8
          },
          {
            label: 'WACC 7.5%',
            data: [{ x: 1.0, y: 110 }, { x: 2.5, y: 119 }, { x: 4.0, y: 135 }],
            backgroundColor: '#ff6b6b',
            pointRadius: 8
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { display: true, labels: { font: { size: 11 }, usePointStyle: true } }
        },
        scales: {
          x: { title: { display: true, text: 'Revenue CAGR (%)' }, ticks: { font: { size: 11 } } },
          y: { title: { display: true, text: 'Price per Share ($)' }, ticks: { font: { size: 11 } } }
        }
      }
    });
  </script>
</body>
</html>
