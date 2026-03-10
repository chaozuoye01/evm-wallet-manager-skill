# index.html — 完整前端代码

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>EVM 钱包资产管理</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=DM+Mono:wght@400;500&family=Outfit:wght@300;400;500;600;700;800;900&display=swap');
:root {
  --bg:#f0f2f7;--white:#fff;--s2:#f8f9fb;--bd:#e4e7ef;--bd2:#c8cdd9;
  --tx:#0d1117;--tx2:#3d4452;--mt:#8b92a5;
  --ac:#0052ff;--acl:#e8f0ff;--ach:#0040cc;
  --gn:#00a86b;--gnl:#e6f9f3;--rd:#e83f3f;--rdl:#fdf0f0;
  --am:#d97706;--aml:#fffbeb;--or:#f97316;--orl:#fff7ed;
  --sh0:0 1px 3px rgba(13,17,23,.07);--sh:0 4px 16px rgba(13,17,23,.09);
  --shl:0 12px 40px rgba(13,17,23,.13);
  --r:12px;--rs:8px;--f:'Outfit',sans-serif;--mo:'DM Mono',monospace;
}
*{margin:0;padding:0;box-sizing:border-box;}
body{background:var(--bg);color:var(--tx);font-family:var(--f);-webkit-font-smoothing:antialiased;}

/* topbar */
.topbar{background:var(--white);border-bottom:1px solid var(--bd);height:62px;padding:0 22px;display:flex;align-items:center;justify-content:space-between;position:sticky;top:0;z-index:100;box-shadow:var(--sh0);}
.logo{display:flex;align-items:center;gap:9px;font-size:15px;font-weight:800;letter-spacing:-.4px;}
.logo-ico{width:31px;height:31px;background:var(--ac);border-radius:8px;display:flex;align-items:center;justify-content:center;font-size:14px;}
.tbr{display:flex;gap:7px;align-items:center;}

/* layout */
.layout{display:grid;grid-template-columns:300px 1fr;min-height:calc(100vh - 62px);}

/* sidebar */
.sidebar{background:var(--white);border-right:1px solid var(--bd);padding:16px;display:flex;flex-direction:column;gap:3px;height:calc(100vh - 62px);position:sticky;top:62px;overflow-y:auto;}
.sec-lbl{font-size:10px;font-weight:700;letter-spacing:2px;text-transform:uppercase;color:var(--mt);padding:0 2px;margin-bottom:8px;display:block;}

/* query all btn */
.btn-qall{width:100%;padding:11px;background:var(--ac);color:white;border:none;border-radius:var(--rs);font-family:var(--f);font-size:13px;font-weight:700;cursor:pointer;transition:all .18s;display:flex;align-items:center;justify-content:center;gap:8px;box-shadow:0 3px 12px rgba(0,82,255,.22);margin-bottom:12px;}
.btn-qall:hover{background:var(--ach);transform:translateY(-1px);}
.btn-qall:disabled{opacity:.6;transform:none;cursor:not-allowed;}

/* 总资产卡片 */
.total-card{background:linear-gradient(135deg,#0052ff,#3b82f6);border-radius:var(--rs);padding:14px;margin-bottom:12px;color:white;display:none;}
.tc-lbl{font-size:10px;font-weight:600;opacity:.8;letter-spacing:.5px;margin-bottom:2px;}
.tc-val{font-size:22px;font-weight:800;font-family:var(--mo);letter-spacing:-1px;}
.tc-sub{font-size:11px;opacity:.7;margin-top:3px;}
.cdist{display:flex;gap:2px;height:4px;border-radius:4px;overflow:hidden;margin-top:9px;}
.cdb{height:100%;}

/* 进度条（批量查询用） */
.progress-panel{background:var(--white);border:1.5px solid var(--ac);border-radius:var(--rs);padding:12px 14px;margin-bottom:12px;display:none;}
.pp-title{font-size:12px;font-weight:700;margin-bottom:8px;display:flex;align-items:center;justify-content:space-between;}
.pp-bar-wrap{height:6px;background:var(--bd);border-radius:4px;overflow:hidden;margin-bottom:8px;}
.pp-bar{height:100%;background:var(--ac);border-radius:4px;transition:width .4s ease;}
.pp-info{display:grid;grid-template-columns:1fr 1fr;gap:4px;}
.pp-item{font-size:10.5px;color:var(--mt);}
.pp-item strong{color:var(--tx);font-family:var(--mo);}
.pp-cur{font-size:10px;color:var(--mt);margin-top:5px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}

/* 限速状态徽章 */
.rate-badge{display:inline-flex;align-items:center;gap:5px;padding:4px 9px;border-radius:20px;font-size:11px;font-weight:700;border:1.5px solid;cursor:pointer;transition:all .15s;}
.rate-badge.ok{background:var(--gnl);color:var(--gn);border-color:var(--gn);}
.rate-badge.warn{background:var(--aml);color:var(--am);border-color:var(--am);}
.rate-badge-dot{width:6px;height:6px;border-radius:50%;animation:pulse 1.5s infinite;}
.rate-badge.ok .rate-badge-dot{background:var(--gn);}
.rate-badge.warn .rate-badge-dot{background:var(--am);}
@keyframes pulse{0%,100%{opacity:1;}50%{opacity:.4;}}

/* wallet items */
.w-item{border:1.5px solid var(--bd);border-radius:var(--rs);padding:10px 12px;cursor:pointer;transition:all .15s;background:var(--white);margin-bottom:5px;}
.w-item:hover{border-color:var(--ac);box-shadow:0 0 0 3px var(--acl);}
.w-item.active{border-color:var(--ac);background:var(--acl);box-shadow:0 0 0 3px var(--acl);}
.w-item.loading-item{border-color:var(--am);background:var(--aml);}
.w-top{display:flex;align-items:center;justify-content:space-between;}
.w-name{font-size:13px;font-weight:700;}
.w-addr{font-family:var(--mo);font-size:10.5px;color:var(--mt);margin-top:2px;}
.w-val{font-family:var(--mo);font-size:12px;font-weight:600;color:var(--gn);margin-top:4px;}
.w-del{opacity:0;transition:opacity .15s;background:none;border:none;cursor:pointer;color:var(--mt);font-size:13px;padding:2px 5px;border-radius:4px;}
.w-del:hover{color:var(--rd);background:var(--rdl);}
.w-item:hover .w-del{opacity:1;}
.w-dots{display:flex;gap:3px;margin-top:4px;flex-wrap:wrap;}
.w-dot{width:6px;height:6px;border-radius:50%;}
.sb-empty{text-align:center;padding:16px 0;color:var(--mt);font-size:12px;}

/* buttons */
.btn{padding:8px 15px;border-radius:var(--rs);border:none;font-family:var(--f);font-weight:600;font-size:13px;cursor:pointer;transition:all .15s;}
.btn-p{background:var(--ac);color:white;}.btn-p:hover{background:var(--ach);}
.btn-s{background:var(--white);color:var(--tx2);border:1.5px solid var(--bd2);}.btn-s:hover{border-color:var(--ac);color:var(--ac);}
.btn-g{background:transparent;color:var(--mt);border:1.5px solid var(--bd);}.btn-g:hover{border-color:var(--bd2);color:var(--tx2);}
.btn-sm{padding:5px 11px;font-size:12px;}
.btn-or{background:var(--orl);color:var(--or);border:1.5px solid var(--or);}.btn-or:hover{background:var(--or);color:white;}

/* inputs */
.inp{background:var(--s2);border:1.5px solid var(--bd);color:var(--tx);padding:8px 11px;border-radius:var(--rs);font-family:var(--f);font-size:13px;width:100%;transition:border-color .15s;}
.inp:focus{outline:none;border-color:var(--ac);background:var(--white);box-shadow:0 0 0 3px var(--acl);}
.inp::placeholder{color:var(--mt);}
.inp-mono{font-family:var(--mo);font-size:11px;}
textarea.inp{resize:vertical;min-height:85px;line-height:1.7;}
.inp-sm{padding:6px 10px;font-size:12px;}

/* range slider */
.range-wrap{position:relative;}
input[type=range]{-webkit-appearance:none;width:100%;height:5px;border-radius:3px;background:var(--bd);outline:none;}
input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;width:16px;height:16px;border-radius:50%;background:var(--ac);cursor:pointer;border:2px solid white;box-shadow:0 1px 4px rgba(0,82,255,.3);}

/* chain grid */
.cgrid{display:grid;grid-template-columns:1fr 1fr;gap:4px;}
.ccb{display:none;}.clbl{display:flex;align-items:center;gap:5px;border:1.5px solid var(--bd);border-radius:6px;padding:5px 7px;cursor:pointer;font-size:11px;font-weight:600;transition:all .12s;color:var(--tx2);overflow:hidden;white-space:nowrap;}
.clbl:hover{border-color:var(--bd2);}
.ccb:checked+.clbl{border-color:var(--ac);color:var(--ac);background:var(--acl);}
.cdot{width:7px;height:7px;border-radius:50%;flex-shrink:0;}
.sel-links{display:flex;gap:8px;margin-bottom:5px;}
.sl{font-size:11px;color:var(--ac);cursor:pointer;font-weight:600;}.sl:hover{text-decoration:underline;}
.divider{height:1px;background:var(--bd);margin:10px 0;}
.form-lbl{font-size:10px;font-weight:700;color:var(--mt);margin-bottom:5px;display:block;letter-spacing:.5px;text-transform:uppercase;}

/* main */
.main{padding:20px;overflow-y:auto;}
.page-head{display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:18px;}
.page-head h2{font-size:20px;font-weight:800;}
.page-head .sub{font-size:12px;color:var(--mt);margin-top:3px;font-family:var(--mo);}
.big-val{font-size:26px;font-weight:800;font-family:var(--mo);color:var(--gn);}
.big-sub{font-size:11px;color:var(--mt);margin-top:2px;text-align:right;}

.stats-row{display:grid;grid-template-columns:repeat(3,1fr);gap:12px;margin-bottom:16px;}
.sc{background:var(--white);border:1px solid var(--bd);border-radius:var(--r);padding:16px 18px;box-shadow:var(--sh0);}
.sc-lbl{font-size:10px;font-weight:700;letter-spacing:1px;text-transform:uppercase;color:var(--mt);margin-bottom:4px;}
.sc-val{font-size:22px;font-weight:800;font-family:var(--mo);letter-spacing:-.8px;}
.c-gn{color:var(--gn);}.c-ac{color:var(--ac);}.c-am{color:var(--am);}

.panel{background:var(--white);border:1px solid var(--bd);border-radius:var(--r);overflow:hidden;box-shadow:var(--sh0);margin-bottom:14px;}
.ph{padding:12px 16px;border-bottom:1px solid var(--bd);display:flex;align-items:center;justify-content:space-between;}
.pt{font-size:14px;font-weight:700;display:flex;align-items:center;gap:8px;}

table{width:100%;border-collapse:collapse;}
th{padding:9px 15px;text-align:left;font-size:9.5px;font-weight:700;letter-spacing:1.5px;text-transform:uppercase;color:var(--mt);background:var(--s2);border-bottom:1px solid var(--bd);}
td{padding:10px 15px;font-size:13px;border-bottom:1px solid var(--bd);}
tr:last-child td{border-bottom:none;}
tr:hover td{background:#fafbff;}
.tm{font-family:var(--mo);font-size:12px;}.tg{color:var(--gn);font-weight:700;}.tmut{color:var(--mt);}.tbd{font-weight:700;}
.tok-n{font-weight:700;font-size:13px;}.tok-a{font-family:var(--mo);font-size:10px;color:var(--mt);margin-top:1px;}

.pct-w{display:flex;align-items:center;gap:7px;}
.pct-t{flex:1;height:4px;background:var(--bd);border-radius:3px;overflow:hidden;max-width:88px;}
.pct-f{height:100%;border-radius:3px;}
.pct-n{font-size:10.5px;font-family:var(--mo);color:var(--mt);min-width:34px;}

.badge{display:inline-block;padding:2px 8px;border-radius:20px;font-size:10px;font-weight:700;}
.b-blue{background:var(--acl);color:var(--ac);}.b-green{background:var(--gnl);color:var(--gn);}

.btn-det{background:var(--acl);color:var(--ac);border:none;padding:4px 11px;border-radius:6px;font-size:11px;font-weight:700;cursor:pointer;font-family:var(--f);transition:all .15s;}
.btn-det:hover{background:var(--ac);color:white;}

/* 限速管理面板 */
.rate-panel{background:var(--white);border:1px solid var(--bd);border-radius:var(--r);padding:18px 20px;box-shadow:var(--sh0);margin-bottom:14px;}
.rate-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin:14px 0;}
.rate-stat{text-align:center;padding:12px 8px;background:var(--s2);border-radius:var(--rs);border:1px solid var(--bd);}
.rate-stat-val{font-size:20px;font-weight:800;font-family:var(--mo);}
.rate-stat-lbl{font-size:10px;color:var(--mt);font-weight:600;letter-spacing:.5px;text-transform:uppercase;margin-top:3px;}
.rate-stat.ok .rate-stat-val{color:var(--gn);}
.rate-stat.info .rate-stat-val{color:var(--ac);}
.rate-stat.warn .rate-stat-val{color:var(--am);}
.rate-stat.neu .rate-stat-val{color:var(--tx2);}
.rate-controls{display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-top:14px;}
.rc-group label{font-size:11px;font-weight:700;color:var(--mt);display:flex;align-items:center;justify-content:space-between;margin-bottom:6px;text-transform:uppercase;letter-spacing:.5px;}
.rc-group label span{color:var(--ac);font-family:var(--mo);font-size:12px;}
.preset-btns{display:flex;gap:5px;margin-top:8px;flex-wrap:wrap;}
.preset-btn{padding:4px 10px;border-radius:5px;font-size:11px;font-weight:700;cursor:pointer;border:1.5px solid var(--bd);background:var(--s2);color:var(--tx2);font-family:var(--f);transition:all .12s;}
.preset-btn:hover{border-color:var(--ac);color:var(--ac);}
.preset-btn.active{background:var(--acl);border-color:var(--ac);color:var(--ac);}
.req-log{max-height:160px;overflow-y:auto;background:var(--s2);border-radius:var(--rs);border:1px solid var(--bd);padding:8px 10px;margin-top:10px;}
.log-item{font-size:11px;font-family:var(--mo);color:var(--mt);padding:2px 0;border-bottom:1px solid var(--bd);display:flex;align-items:center;gap:8px;}
.log-item:last-child{border-bottom:none;}
.log-item.ok .log-dot{background:var(--gn);}
.log-item.err .log-dot{background:var(--rd);}.log-item.err .log-addr{color:var(--rd);}
.log-item.retry .log-dot{background:var(--am);}
.log-dot{width:6px;height:6px;border-radius:50%;flex-shrink:0;}
.log-addr{flex:1;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;}
.log-time{color:var(--ac);min-width:40px;text-align:right;}

/* empty / loading */
.empty-s{display:flex;flex-direction:column;align-items:center;justify-content:center;padding:70px 40px;text-align:center;color:var(--mt);}
.empty-ico{font-size:48px;margin-bottom:14px;opacity:.55;}
.empty-ttl{font-size:17px;font-weight:700;color:var(--tx2);margin-bottom:6px;}
.empty-sub{font-size:13px;line-height:1.6;}
.loading{display:flex;align-items:center;justify-content:center;padding:60px;gap:12px;color:var(--mt);}
.spinner{width:20px;height:20px;border:2px solid var(--bd);border-top-color:var(--ac);border-radius:50%;animation:spin .7s linear infinite;}
@keyframes spin{to{transform:rotate(360deg);}}

/* modal */
.mo{position:fixed;inset:0;background:rgba(13,17,23,.45);display:none;align-items:center;justify-content:center;z-index:1000;backdrop-filter:blur(5px);}
.mo.open{display:flex;}
.modal{background:var(--white);border-radius:16px;padding:24px;width:460px;max-width:92vw;box-shadow:var(--shl);border:1px solid var(--bd);animation:mIn .18s ease;}
@keyframes mIn{from{opacity:0;transform:translateY(8px) scale(.98);}}
.m-title{font-size:17px;font-weight:800;margin-bottom:5px;}
.m-sub{font-size:12.5px;color:var(--mt);margin-bottom:18px;line-height:1.5;}
.m-foot{display:flex;gap:8px;justify-content:flex-end;margin-top:18px;}
.fg{margin-bottom:12px;}

/* toast */
.toast{position:fixed;bottom:20px;right:20px;z-index:2000;background:var(--white);border:1px solid var(--bd);border-radius:10px;padding:10px 15px 10px 12px;font-size:13px;font-weight:600;color:var(--tx);transform:translateY(70px);opacity:0;transition:all .28s cubic-bezier(.34,1.56,.64,1);max-width:300px;box-shadow:var(--shl);display:flex;align-items:center;gap:9px;}
.toast.show{transform:translateY(0);opacity:1;}

/* import hint */
.imp-hint{background:var(--s2);border:1px solid var(--bd);border-radius:var(--rs);padding:9px 11px;font-size:11.5px;color:var(--mt);line-height:1.9;margin-bottom:11px;}
.imp-hint code{font-family:var(--mo);color:var(--ac);background:var(--acl);padding:1px 5px;border-radius:3px;}

::-webkit-scrollbar{width:5px;}
::-webkit-scrollbar-thumb{background:var(--bd2);border-radius:3px;}
</style>
</head>
<body>

<div class="topbar">
  <div class="logo">
    <div class="logo-ico">◈</div>
    EVM Wallet Manager
  </div>
  <div class="tbr">
    <div class="rate-badge ok" id="rateBadge" onclick="showRatePanel()" title="点击查看限速管理">
      <span class="rate-badge-dot"></span>
      <span id="rateLabel">1 req/s</span>
    </div>
    <button class="btn btn-s btn-sm" onclick="openImport()">📥 批量导入</button>
    <button class="btn btn-s btn-sm" onclick="openConfig()">⚙ API 配置</button>
  </div>
</div>

<div class="layout">
  <!-- 左侧 -->
  <div class="sidebar">
    <button class="btn-qall" id="qAllBtn" onclick="queryAll()">
      <span id="qAllIco">⟳</span> 查询所有钱包
    </button>

    <!-- 进度面板 -->
    <div class="progress-panel" id="progressPanel">
      <div class="pp-title">
        <span>⏳ 查询进度</span>
        <span id="ppFrac" style="font-family:var(--mo);color:var(--ac)">0/0</span>
      </div>
      <div class="pp-bar-wrap"><div class="pp-bar" id="ppBar" style="width:0%"></div></div>
      <div class="pp-info">
        <div class="pp-item">已完成 <strong id="ppDone">0</strong></div>
        <div class="pp-item">失败 <strong id="ppErr" style="color:var(--rd)">0</strong></div>
        <div class="pp-item">间隔 <strong id="ppInterval">1s</strong></div>
        <div class="pp-item">最近1min <strong id="ppRpm">0</strong> 次</div>
      </div>
      <div class="pp-cur" id="ppCur">准备中...</div>
    </div>

    <!-- 总资产 -->
    <div class="total-card" id="totalCard">
      <div class="tc-lbl">ALL WALLETS · TOTAL VALUE</div>
      <div class="tc-val" id="grandTotal">$0.00</div>
      <div class="tc-sub" id="tcSub">—</div>
      <div class="cdist" id="cDistBar"></div>
    </div>

    <!-- 钱包列表 -->
    <div>
      <span class="sec-lbl">钱包 <span id="wCount" style="color:var(--ac)">0</span> 个</span>
      <div id="wList"><div class="sb-empty">暂无钱包，点击「批量导入」添加</div></div>
    </div>

    <div class="divider"></div>

    <!-- 手动添加 -->
    <div>
      <span class="sec-lbl">手动添加</span>
      <div style="display:flex;flex-direction:column;gap:7px">
        <input class="inp" id="newLbl" placeholder="备注名称（如：主钱包）">
        <input class="inp inp-mono" id="newAddr" placeholder="0x...">
        <div>
          <div class="sel-links"><span class="sl" onclick="tChains('cg',true)">全选</span><span class="sl" onclick="tChains('cg',false)">清空</span></div>
          <div class="cgrid" id="cg">
            {% for c in chain_list %}
            <input type="checkbox" class="ccb" id="ch_{{c.id}}" value="{{c.id}}" checked>
            <label class="clbl" for="ch_{{c.id}}"><span class="cdot" style="background:{{c.color}}"></span>{{c.name}}</label>
            {% endfor %}
          </div>
        </div>
        <button class="btn btn-p" onclick="addWallet()">+ 添加钱包</button>
      </div>
    </div>
  </div>

  <!-- 主内容 -->
  <div class="main" id="mainContent">
    <div class="empty-s">
      <div class="empty-ico">🔭</div>
      <div class="empty-ttl">欢迎使用 EVM 钱包管理</div>
      <div class="empty-sub">① 配置 API Key &nbsp;② 批量导入地址<br>③ 点「查询所有钱包」一键查看全部资产</div>
    </div>
  </div>
</div>

<!-- 批量导入弹窗 -->
<div class="mo" id="importModal">
  <div class="modal">
    <div class="m-title">📥 批量导入钱包</div>
    <div class="m-sub">每行一个地址，支持以下格式混用</div>
    <div class="imp-hint">
      仅地址：<code>0xAbCd...1234</code><br>
      地址+备注（逗号）：<code>0xAbCd...1234, 主钱包</code><br>
      地址+备注（空格）：<code>0xAbCd...1234 撸毛01</code>
    </div>
    <div class="fg">
      <label class="form-lbl">粘贴地址列表</label>
      <textarea class="inp inp-mono" id="impTxt" rows="8" placeholder="0x1234...abcd, 主钱包&#10;0x5678...efgh&#10;0xXxXx...9999 撸毛01"></textarea>
    </div>
    <div id="impPreview"></div>
    <div class="fg" style="margin-top:10px">
      <label class="form-lbl">查询链</label>
      <div class="sel-links"><span class="sl" onclick="tChains('icg',true)">全选</span><span class="sl" onclick="tChains('icg',false)">清空</span></div>
      <div class="cgrid" id="icg">
        {% for c in chain_list %}
        <input type="checkbox" class="ccb" id="ic_{{c.id}}" value="{{c.id}}" checked>
        <label class="clbl" for="ic_{{c.id}}"><span class="cdot" style="background:{{c.color}}"></span>{{c.name}}</label>
        {% endfor %}
      </div>
    </div>
    <div class="m-foot">
      <button class="btn btn-g" onclick="closeImport()">取消</button>
      <button class="btn btn-p" onclick="doImport()">确认导入</button>
    </div>
  </div>
</div>

<!-- API 配置弹窗 -->
<div class="mo" id="configModal">
  <div class="modal">
    <div class="m-title">⚙ OKX API 配置</div>
    <div class="m-sub">前往 <a href="https://web3.okx.com/build" target="_blank" style="color:var(--ac);font-weight:700">web3.okx.com/build</a> 创建项目获取凭证</div>
    <div class="fg"><label class="form-lbl">API KEY</label><input class="inp" id="cfg_k" placeholder="37c541a1-xxxx-xxxx-xxxx-10fe7a038418"></div>
    <div class="fg"><label class="form-lbl">SECRET KEY</label><input class="inp" id="cfg_s" type="password" placeholder="••••••••••••••••••••"></div>
    <div class="fg"><label class="form-lbl">PASSPHRASE</label><input class="inp" id="cfg_p" type="password" placeholder="••••••••"></div>
    <div class="fg"><label class="form-lbl">PROJECT ID</label><input class="inp" id="cfg_pid" placeholder="86afxxxxxxxxxxxxxxd1bc"></div>
    <div class="m-foot">
      <button class="btn btn-g" onclick="closeConfig()">取消</button>
      <button class="btn btn-p" onclick="saveConfig()">保存配置</button>
    </div>
  </div>
</div>

<div class="toast" id="toast"><span id="tIco"></span><span id="tMsg"></span></div>

<script>
const CHAINS = {{ chain_list | tojson }};
const CM = {};
CHAINS.forEach(c => CM[c.id] = c);
let wallets = {{ wallets | tojson }};
let cache = {};
let reqLog = [];
const MAX_LOG = 50;
let currentSSE = null;

// ── 初始化 ──
(function(){
  const cfg = {{ api_config | tojson }};
  document.getElementById('cfg_k').value = cfg.api_key||'';
  document.getElementById('cfg_s').value = cfg.secret_key||'';
  document.getElementById('cfg_p').value = cfg.passphrase||'';
  document.getElementById('cfg_pid').value = cfg.project_id||'';
  renderList();
  refreshRateStatus();
  setInterval(refreshRateStatus, 3000);
})();

// ── Toast ──
function toast(msg, type='ok'){
  const icons={ok:'✅',err:'❌',info:'ℹ️',warn:'⚠️'};
  document.getElementById('tIco').textContent = icons[type]||'✅';
  document.getElementById('tMsg').textContent = msg;
  const el = document.getElementById('toast');
  el.className='toast show';
  setTimeout(()=>el.classList.remove('show'),3200);
}

// ── 限速状态刷新 ──
async function refreshRateStatus(){
  try {
    const r = await fetch('/api/rate-status');
    const d = await r.json();
    const badge = document.getElementById('rateBadge');
    const lbl = document.getElementById('rateLabel');
    lbl.textContent = `${d.rps_limit} req/s`;
    badge.className = 'rate-badge ' + (d.rpm_actual > 55 ? 'warn' : 'ok');
    // 更新进度面板
    document.getElementById('ppInterval').textContent = d.interval + 's';
    document.getElementById('ppRpm').textContent = d.rpm_actual;
  } catch(e){}
}

// ── 显示限速管理面板 ──
function showRatePanel(){
  fetch('/api/rate-config').then(r=>r.json()).then(d=>{
    renderRatePanel(d);
  });
}

function renderRatePanel(d){
  const main = document.getElementById('mainContent');
  main.innerHTML = `
  <div class="page-head">
    <div><h2>⚡ 限速管理</h2><div class="sub">OKX API 请求频率控制与实时监控</div></div>
  </div>

  <div class="rate-panel">
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:4px">
      <div class="pt">📊 实时请求统计</div>
      <button class="btn btn-s btn-sm" onclick="showRatePanel()">🔄 刷新</button>
    </div>
    <div class="rate-grid">
      <div class="rate-stat info">
        <div class="rate-stat-val" id="rs-limit">${d.rps_limit}</div>
        <div class="rate-stat-lbl">请求限速 (req/s)</div>
      </div>
      <div class="rate-stat ok">
        <div class="rate-stat-val" id="rs-rpm">${d.rpm_actual}</div>
        <div class="rate-stat-lbl">最近1min 请求数</div>
      </div>
      <div class="rate-stat neu">
        <div class="rate-stat-val" id="rs-total">${d.total_requests}</div>
        <div class="rate-stat-lbl">累计请求总数</div>
      </div>
      <div class="rate-stat warn">
        <div class="rate-stat-val" id="rs-retry">${d.total_retries}</div>
        <div class="rate-stat-lbl">累计重试次数</div>
      </div>
    </div>

    <div class="rate-controls">
      <div class="rc-group">
        <label>请求间隔 (秒) <span id="intervalVal">${d.interval}</span>s</label>
        <input type="range" min="0.2" max="5" step="0.1" value="${d.interval}" id="intervalSlider"
          oninput="document.getElementById('intervalVal').textContent=parseFloat(this.value).toFixed(1)">
        <div class="preset-btns" id="presetBtns">
          <span class="preset-btn ${d.interval==0.5?'active':''}" onclick="setPreset(0.5)">0.5s</span>
          <span class="preset-btn ${d.interval==1?'active':''}" onclick="setPreset(1)">1s</span>
          <span class="preset-btn ${d.interval==2?'active':''}" onclick="setPreset(2)">2s</span>
          <span class="preset-btn ${d.interval==3?'active':''}" onclick="setPreset(3)">3s</span>
          <span class="preset-btn ${d.interval==5?'active':''}" onclick="setPreset(5)">5s</span>
        </div>
      </div>
      <div class="rc-group">
        <label>失败重试次数 <span id="retryVal">${d.max_retries}</span>次</label>
        <input type="range" min="1" max="10" step="1" value="${d.max_retries}" id="retrySlider"
          oninput="document.getElementById('retryVal').textContent=this.value">
        <div style="margin-top:8px">
          <button class="btn btn-p btn-sm" onclick="saveRateConfig()">💾 保存配置</button>
          <span style="font-size:11px;color:var(--mt);margin-left:8px">重启后自动加载</span>
        </div>
        <div style="margin-top:6px;font-size:11px;color:var(--mt);line-height:1.7">
          错误发生时自动退避重试<br>间隔 = 1.5s × (重试次数+1)
        </div>
      </div>
    </div>
  </div>

  <div class="panel">
    <div class="ph"><div class="pt">📋 请求日志（最近${MAX_LOG}条）</div>
      <button class="btn btn-s btn-sm" onclick="clearLog()">清空</button>
    </div>
    <div class="req-log" id="reqLog">
      ${reqLog.length ? reqLog.map(renderLogItem).join('') : '<div style="text-align:center;padding:20px;color:var(--mt);font-size:12px">暂无请求记录</div>'}
    </div>
  </div>`;
}

function renderLogItem(item){
  const cls = item.type==='err'?'err':item.type==='retry'?'retry':'ok';
  return `<div class="log-item ${cls}">
    <span class="log-dot"></span>
    <span class="log-addr">${item.label||item.addr}</span>
    <span style="font-size:10px;color:var(--mt)">${item.msg||''}</span>
    <span class="log-time">${item.time}</span>
  </div>`;
}

function addLog(type, addr, label, msg=''){
  const now = new Date().toLocaleTimeString('zh',{hour:'2-digit',minute:'2-digit',second:'2-digit'});
  reqLog.unshift({type, addr, label, msg, time:now});
  if(reqLog.length > MAX_LOG) reqLog.pop();
}

function clearLog(){ reqLog=[]; toast('日志已清空','info'); }

function setPreset(val){
  document.getElementById('intervalSlider').value = val;
  document.getElementById('intervalVal').textContent = val.toFixed(1);
  document.querySelectorAll('.preset-btn').forEach(b=>{
    b.classList.toggle('active', parseFloat(b.textContent)===val);
  });
}

async function saveRateConfig(){
  const interval = parseFloat(document.getElementById('intervalSlider').value);
  const retries = parseInt(document.getElementById('retrySlider').value);
  const r = await fetch('/api/rate-config',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({interval,max_retries:retries})});
  const d = await r.json();
  if(d.success){
    toast(`限速已更新：每 ${d.interval}s 1次，最多重试 ${d.max_retries} 次`);
    refreshRateStatus();
  }
}

// ── API 配置 ──
function openConfig(){document.getElementById('configModal').classList.add('open');}
function closeConfig(){document.getElementById('configModal').classList.remove('open');}
async function saveConfig(){
  const cfg={api_key:document.getElementById('cfg_k').value.trim(),secret_key:document.getElementById('cfg_s').value.trim(),passphrase:document.getElementById('cfg_p').value.trim(),project_id:document.getElementById('cfg_pid').value.trim()};
  await fetch('/api/save-config',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify(cfg)});
  closeConfig(); toast('API 配置已保存');
}

// ── 批量导入 ──
function openImport(){document.getElementById('importModal').classList.add('open');}
function closeImport(){document.getElementById('importModal').classList.remove('open');document.getElementById('impTxt').value='';document.getElementById('impPreview').innerHTML='';}
function tChains(gid,val){document.querySelectorAll(`#${gid} .ccb`).forEach(c=>c.checked=val);}

function parseAddrs(text){
  return text.split('\n').map(l=>l.trim()).filter(Boolean).map((line,i)=>{
    let addr,lbl;
    if(line.includes(',')){ [addr,...rest]=line.split(',');lbl=rest.join(',').trim(); }
    else { const p=line.split(/\s+/);addr=p[0];lbl=p.slice(1).join(' '); }
    addr=addr.trim().toLowerCase(); lbl=lbl?lbl.trim():`钱包 ${i+1}`;
    return (addr.startsWith('0x')&&addr.length===42)?{address:addr,label:lbl}:null;
  }).filter(Boolean);
}

document.getElementById('impTxt').addEventListener('input',function(){
  const p=parseAddrs(this.value);
  const el=document.getElementById('impPreview');
  if(!p.length){el.innerHTML='';return;}
  el.innerHTML=`<div style="background:var(--gnl);border:1px solid var(--gn);border-radius:8px;padding:8px 11px;font-size:12px;color:var(--gn);font-weight:600;margin-top:8px">✓ 识别到 ${p.length} 个有效地址</div>`;
});

async function doImport(){
  const parsed=parseAddrs(document.getElementById('impTxt').value);
  if(!parsed.length) return toast('未识别到有效地址','err');
  const chains=[...document.querySelectorAll('#icg .ccb:checked')].map(c=>c.value);
  if(!chains.length) return toast('请至少选择一条链','err');
  let added=0,skip=0;
  for(const w of parsed){
    if(wallets.some(e=>e.address.toLowerCase()===w.address)){skip++;continue;}
    const r=await fetch('/api/wallets',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({address:w.address,label:w.label,chains})});
    const d=await r.json();
    d.success?added++:skip++;
  }
  const r2=await fetch('/api/wallets'); wallets=await r2.json();
  renderList(); closeImport();
  toast(`导入完成：新增 ${added} 个${skip?`，跳过 ${skip} 个`:''}`)
}

// ── 渲染钱包列表 ──
function renderList(){
  document.getElementById('wCount').textContent=wallets.length;
  const el=document.getElementById('wList');
  if(!wallets.length){el.innerHTML='<div class="sb-empty">暂无钱包，点击「批量导入」添加</div>';return;}
  el.innerHTML=wallets.map((w,i)=>{
    const res=cache[w.address.toLowerCase()];
    const short=w.address.slice(0,6)+'...'+w.address.slice(-4);
    const valHtml=res?`<div class="w-val">≈ ${res.total_usd_display}</div>`:'';
    const dots=(w.chains||[]).slice(0,8).map(id=>{const c=CM[id];return c?`<span class="w-dot" style="background:${c.color}" title="${c.name}"></span>`:''}).join('');
    return `<div class="w-item" id="wi${i}" onclick="queryOne('${w.address}',${i})">
      <div class="w-top"><div class="w-name">${esc(w.label)}</div>
      <button class="w-del" onclick="delWallet(event,'${w.address}')" title="删除">✕</button></div>
      <div class="w-addr">${short}</div>${valHtml}
      <div class="w-dots">${dots}</div>
    </div>`;
  }).join('');
}

function esc(s){return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');}

async function addWallet(){
  const lbl=document.getElementById('newLbl').value.trim();
  const addr=document.getElementById('newAddr').value.trim().toLowerCase();
  const chains=[...document.querySelectorAll('#cg .ccb:checked')].map(c=>c.value);
  if(!addr.startsWith('0x')||addr.length!==42) return toast('请输入有效 EVM 地址','err');
  if(!chains.length) return toast('请至少选择一条链','err');
  const r=await fetch('/api/wallets',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({address:addr,label:lbl||`钱包${wallets.length+1}`,chains})});
  const d=await r.json();
  if(!d.success) return toast(d.error||'添加失败','err');
  document.getElementById('newAddr').value=''; document.getElementById('newLbl').value='';
  const r2=await fetch('/api/wallets'); wallets=await r2.json();
  renderList(); toast(`已添加 ${lbl||addr.slice(0,10)+'...'}`);
}

async function delWallet(e,addr){
  e.stopPropagation();
  if(!confirm('确认删除？')) return;
  await fetch(`/api/wallets/${addr}`,{method:'DELETE'});
  wallets=wallets.filter(w=>w.address.toLowerCase()!==addr.toLowerCase());
  delete cache[addr.toLowerCase()];
  renderList(); toast('已删除');
}

// ── 查询单个 ──
async function queryOne(addr,idx){
  document.querySelectorAll('.w-item').forEach(e=>e.classList.remove('active'));
  document.getElementById(`wi${idx}`)?.classList.add('active');
  const main=document.getElementById('mainContent');
  main.innerHTML=`<div class="loading"><div class="spinner"></div> 查询中...</div>`;
  const r=await fetch(`/api/query/${addr}`);
  const d=await r.json();
  if(!d.success){
    addLog('err',addr,wallets[idx]?.label||addr,d.error);
    main.innerHTML=`<div class="empty-s"><div class="empty-ico">⚠️</div><div class="empty-ttl">查询失败</div><div class="empty-sub" style="color:var(--rd)">${d.error}</div></div>`;
    return;
  }
  addLog('ok',addr,wallets[idx]?.label||addr,'查询成功');
  cache[addr.toLowerCase()]=d.data;
  renderList(); renderDetail(d.data,addr,wallets[idx]?.label||'未命名');
  refreshRateStatus();
}

// ── 查询所有（SSE 流式）──
function queryAll(){
  if(!wallets.length) return toast('请先添加钱包','err');
  if(currentSSE){currentSSE.close();currentSSE=null;}

  const btn=document.getElementById('qAllBtn');
  const ico=document.getElementById('qAllIco');
  btn.disabled=true; ico.style.animation='spin .7s linear infinite';

  const pp=document.getElementById('progressPanel');
  pp.style.display='block';
  document.getElementById('ppBar').style.width='0%';
  document.getElementById('ppFrac').textContent='0/0';
  document.getElementById('ppDone').textContent='0';
  document.getElementById('ppErr').textContent='0';
  document.getElementById('ppCur').textContent='准备中...';

  let done=0, errs=0, total=0;

  currentSSE = new EventSource('/api/query-all-stream');

  currentSSE.onmessage = function(e){
    const d = JSON.parse(e.data);

    if(d.rate_status){
      document.getElementById('ppInterval').textContent = d.rate_status.interval+'s';
      document.getElementById('ppRpm').textContent = d.rate_status.rpm_actual;
      document.getElementById('rateLabel').textContent = d.rate_status.rps_limit+' req/s';
    }

    if(d.type==='start'){
      total=d.total;
      document.getElementById('ppFrac').textContent=`0/${total}`;
    }
    else if(d.type==='querying'){
      document.getElementById('ppCur').textContent=`⏳ ${d.label} (${d.address.slice(0,8)}...)`;
      document.getElementById('ppBar').style.width=d.pct+'%';
      document.querySelectorAll('.w-item').forEach(el=>el.classList.remove('loading-item'));
      // 标记当前正在查询的条目
      const idx=wallets.findIndex(w=>w.address.toLowerCase()===d.address.toLowerCase());
      if(idx>=0) document.getElementById(`wi${idx}`)?.classList.add('loading-item');
    }
    else if(d.type==='done_one'){
      done++;
      document.getElementById('ppDone').textContent=done;
      document.getElementById('ppFrac').textContent=`${done+errs}/${total}`;
      document.getElementById('ppBar').style.width=d.pct+'%';
      document.getElementById('ppCur').textContent=`✅ ${d.label} → ${d.total_usd}`;
      addLog('ok',d.address,d.label,d.total_usd);
      const idx=wallets.findIndex(w=>w.address.toLowerCase()===d.address.toLowerCase());
      if(idx>=0) document.getElementById(`wi${idx}`)?.classList.remove('loading-item');
    }
    else if(d.type==='error_one'){
      errs++;
      document.getElementById('ppErr').textContent=errs;
      document.getElementById('ppFrac').textContent=`${done+errs}/${total}`;
      document.getElementById('ppBar').style.width=d.pct+'%';
      document.getElementById('ppCur').textContent=`❌ ${d.label} 失败`;
      addLog('err',d.address,d.label,d.error);
      const idx=wallets.findIndex(w=>w.address.toLowerCase()===d.address.toLowerCase());
      if(idx>=0) document.getElementById(`wi${idx}`)?.classList.remove('loading-item');
    }
    else if(d.type==='error'){
      toast(d.msg,'err');
      btn.disabled=false; ico.style.animation='';
      currentSSE.close(); currentSSE=null;
    }
    else if(d.type==='complete'){
      d.results.forEach(rr=>{ if(rr.data) cache[rr.address.toLowerCase()]=rr.data; });
      document.getElementById('grandTotal').textContent=d.grand_total_display;
      document.getElementById('tcSub').textContent=`${wallets.length} 个钱包 · ${Object.keys(d.chain_totals).length} 条链`;
      renderCDist(d.chain_totals,d.grand_total);
      document.getElementById('totalCard').style.display='block';
      document.getElementById('ppCur').textContent=`🎉 全部完成！成功 ${done}，失败 ${errs}`;
      renderList(); renderAllDetail(d);
      btn.disabled=false; ico.style.animation='';
      toast(`查询完成：${done} 成功${errs?`，${errs} 失败`:''}，共 ${d.grand_total_display}`);
      refreshRateStatus();
      currentSSE.close(); currentSSE=null;
    }
  };

  currentSSE.onerror = function(){
    btn.disabled=false; ico.style.animation='';
    currentSSE.close(); currentSSE=null;
    toast('连接中断，请重试','err');
  };
}

function renderCDist(ct,total){
  document.getElementById('cDistBar').innerHTML=Object.entries(ct).slice(0,8).map(([id,info])=>{
    const pct=total>0?(info.usd/total*100):0;
    return `<div class="cdb" style="width:${pct}%;background:rgba(255,255,255,.55)" title="${info.name}: ${info.usd_display}"></div>`;
  }).join('');
}

function renderDetail(res,addr,lbl){
  const cc=Object.keys(res.by_chain).length;
  const tk=Object.values(res.by_chain).reduce((s,c)=>s+c.tokens.length,0);
  let html=`<div class="page-head">
    <div><h2>${esc(lbl)}</h2><div class="sub">${addr}</div></div>
    <div style="text-align:right"><div class="big-val">${res.total_usd_display}</div><div class="big-sub">${cc} 条链 · ${tk} 种 Token</div></div>
  </div>
  <div class="stats-row">
    <div class="sc"><div class="sc-lbl">总价值 (USD)</div><div class="sc-val c-gn">${res.total_usd_display}</div></div>
    <div class="sc"><div class="sc-lbl">活跃链</div><div class="sc-val c-ac">${cc}</div></div>
    <div class="sc"><div class="sc-lbl">Token 种类</div><div class="sc-val c-am">${tk}</div></div>
  </div>`;
  if(cc>1){
    html+=`<div class="panel"><div class="ph"><div class="pt">📊 链分布</div></div>
    <table><thead><tr><th>链</th><th>价值</th><th>占比</th><th>Token</th></tr></thead><tbody>`;
    for(const[cid,cd]of Object.entries(res.by_chain)){
      const pct=res.total_usd>0?(cd.chain_usd/res.total_usd*100):0;
      html+=`<tr><td><span style="display:inline-flex;align-items:center;gap:7px"><span style="width:8px;height:8px;border-radius:50%;background:${cd.chain_color};display:inline-block"></span><strong>${cd.chain_name}</strong></span></td>
      <td class="tm tg">${cd.chain_usd_display}</td>
      <td><div class="pct-w"><div class="pct-t"><div class="pct-f" style="width:${pct}%;background:${cd.chain_color}"></div></div><span class="pct-n">${pct.toFixed(1)}%</span></div></td>
      <td class="tmut">${cd.tokens.length}</td></tr>`;
    }
    html+=`</tbody></table></div>`;
  }
  for(const[cid,cd]of Object.entries(res.by_chain)){
    const pct=res.total_usd>0?(cd.chain_usd/res.total_usd*100):0;
    html+=`<div class="panel"><div class="ph">
      <div class="pt"><span style="width:10px;height:10px;border-radius:50%;background:${cd.chain_color};display:inline-block"></span>${cd.chain_name}<span class="badge b-blue">${cd.tokens.length} Token</span></div>
      <div style="display:flex;align-items:center;gap:10px"><span class="tm tg tbd">${cd.chain_usd_display}</span><span style="font-size:11px;color:var(--mt)">${pct.toFixed(1)}%</span></div>
    </div>
    <table><thead><tr><th>Token</th><th>数量</th><th>单价</th><th>USD 价值</th></tr></thead><tbody>
    ${cd.tokens.map(t=>`<tr>
      <td><div class="tok-n">${esc(t.symbol)}</div><div class="tok-a">${t.token_address?t.token_address.slice(0,8)+'…'+t.token_address.slice(-6):'原生代币'}</div></td>
      <td class="tm">${t.balance}</td><td class="tm tmut">${t.price}</td>
      <td class="tm tg tbd">${t.usd_display}</td>
    </tr>`).join('')}
    </tbody></table></div>`;
  }
  if(!cc) html+=`<div class="empty-s"><div class="empty-ico">📭</div><div class="empty-ttl">该地址暂无资产</div></div>`;
  document.getElementById('mainContent').innerHTML=html;
}

function renderAllDetail(data){
  let html=`<div class="page-head">
    <div><h2>所有钱包总览</h2><div class="sub">${wallets.length} 个钱包 · ${Object.keys(data.chain_totals).length} 条链有资产</div></div>
    <div style="text-align:right"><div class="big-val">${data.grand_total_display}</div><div class="big-sub">全部资产总价值</div></div>
  </div>
  <div class="stats-row">
    <div class="sc"><div class="sc-lbl">总资产 (USD)</div><div class="sc-val c-gn">${data.grand_total_display}</div></div>
    <div class="sc"><div class="sc-lbl">钱包数量</div><div class="sc-val c-ac">${wallets.length}</div></div>
    <div class="sc"><div class="sc-lbl">活跃链数量</div><div class="sc-val c-am">${Object.keys(data.chain_totals).length}</div></div>
  </div>
  <div class="panel"><div class="ph"><div class="pt">🌐 跨链资产分布</div></div>
  <table><thead><tr><th>链</th><th>总价值</th><th>占比</th></tr></thead><tbody>`;
  for(const[cid,info]of Object.entries(data.chain_totals)){
    const pct=data.grand_total>0?(info.usd/data.grand_total*100):0;
    html+=`<tr><td><span style="display:inline-flex;align-items:center;gap:8px"><span style="width:8px;height:8px;border-radius:50%;background:${info.color};display:inline-block"></span><strong>${info.name}</strong></span></td>
    <td class="tm tg">${info.usd_display}</td>
    <td><div class="pct-w"><div class="pct-t"><div class="pct-f" style="width:${pct}%;background:${info.color}"></div></div><span class="pct-n">${pct.toFixed(1)}%</span></div></td></tr>`;
  }
  html+=`</tbody></table></div>
  <div class="panel"><div class="ph"><div class="pt">💼 各钱包明细</div></div>
  <table><thead><tr><th>备注</th><th>地址</th><th>链分布</th><th>总价值</th><th></th></tr></thead><tbody>`;
  data.results.forEach((rr,i)=>{
    if(rr.error){
      html+=`<tr><td><strong>${esc(rr.label)}</strong></td><td class="tm tmut">${rr.address.slice(0,8)}…${rr.address.slice(-6)}</td><td colspan="2" style="color:var(--rd);font-size:12px">失败：${rr.error}</td><td></td></tr>`;
    } else {
      const dots=Object.keys(rr.data.by_chain).slice(0,6).map(id=>{const c=CM[id];return c?`<span style="width:8px;height:8px;border-radius:50%;background:${c.color};display:inline-block;margin-right:2px" title="${c.name}"></span>`:''}).join('');
      html+=`<tr><td><strong>${esc(rr.label)}</strong></td><td class="tm tmut">${rr.address.slice(0,8)}…${rr.address.slice(-6)}</td><td>${dots}</td><td class="tm tg tbd">${rr.data.total_usd_display}</td><td><button class="btn-det" onclick="queryOne('${rr.address}',${i})">详情 →</button></td></tr>`;
    }
  });
  html+=`</tbody></table></div>`;
  document.getElementById('mainContent').innerHTML=html;
}

['configModal','importModal'].forEach(id=>{
  document.getElementById(id).addEventListener('click',function(e){if(e.target===this)this.classList.remove('open');});
});
</script>
</body>
</html>
```
