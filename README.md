<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>剣道会 出欠・入館・掃除当番管理</title>
<style>
  :root {
    --font-gothic: "Hiragino Kaku Gothic ProN", "Hiragino Sans", "Yu Gothic", "Meiryo", sans-serif;
    --bg: #f4f5f7;
    --card: #ffffff;
    --text: #1f2937;
    --muted: #6b7280;
    --primary: #1e3a8a;
    --primary-light: #eff6ff;
    --accent: #0f766e;
    --danger: #dc2626;
    --warning-bg: #fef9c3;
    --warning-border: #eab308;
    --retired-bg: #fee2e2;
    --retired-border: #ef4444;
    --border: #d1d5db;
  }

  * {
    box-sizing: border-box;
    font-family: var(--font-gothic);
  }

  body {
    margin: 0;
    padding: 0 0 84px 0;
    background: var(--bg);
    color: var(--text);
    font-size: 15px;
    line-height: 1.45;
  }

  header.app-header {
    background: var(--primary);
    color: #fff;
    padding: 12px 16px;
    position: sticky;
    top: 0;
    z-index: 30;
    box-shadow: 0 2px 6px rgba(0,0,0,0.12);
  }

  header.app-header h1 {
    margin: 0;
    font-size: 18px;
    font-weight: 700;
    letter-spacing: 0.04em;
  }

  .edit-banner {
    display: none;
    background: #fef3c7;
    color: #92400e;
    border-bottom: 2px solid #f59e0b;
    padding: 8px 14px;
    font-weight: 700;
    font-size: 14px;
    justify-content: space-between;
    align-items: center;
    gap: 8px;
  }

  .edit-banner.active {
    display: flex;
  }

  .container {
    max-width: 920px;
    margin: 0 auto;
    padding: 12px;
  }

  .tab-panel {
    display: none;
  }

  .tab-panel.active {
    display: block;
  }

  .card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 14px;
    margin-bottom: 12px;
    box-shadow: 0 1px 2px rgba(0,0,0,0.04);
  }

  .card-title {
    font-size: 16px;
    font-weight: 700;
    margin: 0 0 10px 0;
    padding-bottom: 6px;
    border-bottom: 2px solid #e5e7eb;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    gap: 6px;
  }

  .grid-2 {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 10px;
  }

  .grid-3 {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
    gap: 10px;
  }

  label.field-label {
    display: block;
    font-size: 12px;
    font-weight: 700;
    color: var(--muted);
    margin-bottom: 4px;
  }

  input[type="text"],
  input[type="number"],
  input[type="date"],
  select,
  textarea {
    width: 100%;
    padding: 8px 10px;
    border: 1px solid #9ca3af;
    border-radius: 6px;
    font-size: 15px;
    background: #fff;
    color: var(--text);
  }

  button {
    font-family: var(--font-gothic);
    cursor: pointer;
    border-radius: 8px;
    border: 1px solid transparent;
    padding: 9px 14px;
    font-size: 14px;
    font-weight: 700;
    transition: opacity 0.15s, background 0.15s;
  }

  button:active {
    opacity: 0.8;
  }

  .btn-primary {
    background: var(--primary);
    color: #fff;
  }

  .btn-accent {
    background: var(--accent);
    color: #fff;
  }

  .btn-danger {
    background: var(--danger);
    color: #fff;
  }

  .btn-outline {
    background: #fff;
    color: var(--text);
    border-color: #9ca3af;
  }

  .btn-sm {
    padding: 5px 10px;
    font-size: 13px;
  }

  /* 生徒行（横並びチェック） */
  .student-row {
    border: 1px solid #e5e7eb;
    border-radius: 8px;
    padding: 10px;
    margin-bottom: 8px;
    background: #fafafa;
  }

  .student-row.is-retired {
    background: #f3f4f6;
    color: #9ca3af;
  }

  .student-top {
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 6px;
    margin-bottom: 8px;
  }

  .student-name-wrap {
    display: flex;
    align-items: center;
    gap: 6px;
    font-weight: 700;
    font-size: 16px;
  }

  .badge-kento {
    display: inline-block;
    background: var(--warning-bg);
    color: #854d0e;
    border: 2px solid var(--warning-border);
    border-radius: 4px;
    padding: 0 5px;
    font-size: 12px;
    font-weight: 700;
  }

  .badge-taikai {
    display: inline-block;
    background: var(--retired-bg);
    color: #991b1b;
    border: 2px solid var(--retired-border);
    border-radius: 4px;
    padding: 0 5px;
    font-size: 12px;
    font-weight: 700;
  }

  .checks-row {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    align-items: center;
  }

  .chk-pill {
    display: inline-flex;
    align-items: center;
    gap: 5px;
    background: #fff;
    border: 1px solid #cbd5e1;
    border-radius: 6px;
    padding: 5px 9px;
    font-size: 13px;
    font-weight: 700;
    user-select: none;
  }

  .chk-pill input[type="checkbox"] {
    width: 17px;
    height: 17px;
    margin: 0;
    accent-color: var(--primary);
  }

  .chk-pill.public-absence {
    border-color: #0284c7;
    background: #f0f9ff;
    color: #0369a1;
  }

  .chk-pill.teacher-role {
    border-color: #7c3aed;
    background: #f5f3ff;
    color: #5b21b6;
  }

  /* 折りたたみアコーディオン */
  .accordion-box {
    border: 1px solid var(--border);
    border-radius: 8px;
    margin-bottom: 10px;
    background: #fff;
    overflow: hidden;
  }

  .accordion-header {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: #f1f5f9;
    color: var(--text);
    border: none;
    border-radius: 0;
    padding: 12px 14px;
    font-size: 15px;
    font-weight: 700;
    text-align: left;
  }

  .accordion-body {
    display: none;
    padding: 12px;
    border-top: 1px solid #e5e7eb;
  }

  .accordion-body.open {
    display: block;
  }

  /* 掃除当番 */
  .duty-boxes {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 8px;
    margin: 10px 0;
  }

  .duty-slot {
    background: #ecfdf5;
    border: 2px solid #10b981;
    border-radius: 8px;
    padding: 10px 6px;
    text-align: center;
  }

  .duty-slot .slot-num {
    font-size: 11px;
    font-weight: 700;
    color: #047857;
  }

  .duty-slot .slot-name {
    font-size: 15px;
    font-weight: 700;
    margin-top: 2px;
  }

  /* 横並び集計表 */
  .table-wrap {
    overflow-x: auto;
  }

  table.tally-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 13px;
    text-align: center;
    min-width: 680px;
  }

  table.tally-table th,
  table.tally-table td {
    border: 1px solid #9ca3af;
    padding: 6px 4px;
    font-weight: 700;
  }

  table.tally-table th {
    background: #e2e8f0;
  }

  table.tally-table tr.total-row td {
    background: #fef9c3;
    font-size: 14px;
  }

  /* PDF（A4縦）プレビュー・印刷領域 */
  .pdf-sheet {
    background: #fff;
    border: 1px solid #9ca3af;
    padding: 24px 28px;
    margin-top: 12px;
    color: #000;
  }

  .pdf-date-line {
    font-size: 14px;
    font-weight: 700;
    margin-bottom: 24px; /* 2行相当の余白 */
  }

  .pdf-title {
    text-align: center;
    font-size: 22px;
    font-weight: 700;
    margin: 0 0 22px 0;
    letter-spacing: 0.08em;
  }

  .pdf-columns {
    display: grid;
    grid-template-columns: 1fr 1.3fr;
    gap: 24px;
    align-items: start;
  }

  .pdf-col-heading {
    font-size: 15px;
    font-weight: 700;
    border-bottom: 1.5px solid #000;
    padding-bottom: 4px;
    margin-bottom: 10px;
  }

  .pdf-item {
    font-size: 14px;
    font-weight: 700;
    margin-bottom: 6px;
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .pdf-absent-mark {
    color: #dc2626;
    border: 2px solid #dc2626;
    border-radius: 4px;
    padding: 0 5px;
    font-size: 13px;
    font-weight: 700;
    line-height: 1.2;
  }

  .pdf-public-mark {
    color: #0369a1;
    border: 1.5px solid #0369a1;
    border-radius: 4px;
    padding: 0 4px;
    font-size: 12px;
    font-weight: 700;
  }

  .pdf-grade-group {
    margin-bottom: 12px;
  }

  .pdf-grade-header {
    font-weight: 700;
    font-size: 14px;
    margin-bottom: 3px;
  }

  .pdf-student-names {
    padding-left: 12px;
    font-size: 14px;
    font-weight: 700;
  }

  /* 下部固定4タブ */
  nav.bottom-tabs {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    background: #fff;
    border-top: 2px solid #cbd5e1;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    z-index: 40;
  
