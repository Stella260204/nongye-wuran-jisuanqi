<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>农业面源污染负荷 · 互动计算器</title>
<style>
  body{margin:0;font-family:-apple-system,"PingFang SC","Microsoft YaHei",sans-serif;background:#f3f6f4;color:#1c2b24;}
  .wrap{max-width:880px;margin:0 auto;padding:18px;}
  .hd{display:flex;flex-wrap:wrap;align-items:center;justify-content:space-between;gap:10px;margin-bottom:14px;}
  .hd h1{font-size:19px;margin:0;color:#1b4332;}
  .hd .sub{font-size:12px;color:#6b7f74;margin-top:3px;}
  .seg{display:flex;background:#e4ece7;border-radius:10px;padding:3px;gap:3px;}
  .seg button{border:0;background:transparent;padding:7px 16px;border-radius:8px;font-size:13px;cursor:pointer;color:#52705f;font-weight:600;}
  .seg button.on{background:#2f9e44;color:#fff;box-shadow:0 1px 3px rgba(0,0,0,.15);}
  .card{background:#fff;border-radius:14px;padding:14px 16px;margin-bottom:14px;box-shadow:0 1px 4px rgba(27,67,50,.08);}
  .card h2{font-size:15px;margin:0 0 2px;color:#1b4332;display:flex;justify-content:space-between;align-items:baseline;gap:8px;}
  .card .res{font-size:13px;font-weight:700;color:#2f9e44;white-space:nowrap;}
  .formula{font-size:12px;color:#8aa198;background:#f0f5f1;border-radius:8px;padding:6px 10px;margin:8px 0 10px;font-family:Georgia,serif;}
  table{width:100%;border-collapse:collapse;font-size:13px;}
  th{text-align:left;color:#6b7f74;font-weight:600;padding:4px 6px;border-bottom:1px solid #e7eee9;font-size:12px;}
  td{padding:5px 6px;border-bottom:1px solid #f1f5f2;}
  td input{width:100%;box-sizing:border-box;border:1px solid #d5e0d8;border-radius:7px;padding:6px 8px;font-size:13px;text-align:right;background:#fbfdfc;color:#1c2b24;}
  td input:focus{outline:2px solid #95d5b2;border-color:#2f9e44;}
  .unit{font-size:11px;color:#9db3a6;white-space:nowrap;}
  .row2{display:grid;grid-template-columns:1fr 1fr;gap:10px;}
  .field label{display:block;font-size:12px;color:#52705f;margin-bottom:4px;}
  .field input,.field select{width:100%;box-sizing:border-box;border:1px solid #d5e0d8;border-radius:7px;padding:7px 9px;font-size:13px;text-align:right;background:#fbfdfc;color:#1c2b24;}
  .field select{text-align:left;}
  .sum{background:linear-gradient(135deg,#1b4332,#2f9e44);color:#fff;}
  .sum h2{color:#fff;}
  .total{font-size:30px;font-weight:800;margin:6px 0 2px;}
  .total small{font-size:13px;font-weight:500;opacity:.85;}
  .bar-row{display:flex;align-items:center;gap:8px;margin-top:8px;font-size:12px;}
  .bar-row .lbl{width:86px;opacity:.9;flex-shrink:0;}
  .bar-row .track{flex:1;background:rgba(255,255,255,.18);border-radius:6px;height:14px;overflow:hidden;}
  .bar-row .fill{height:100%;border-radius:6px;background:#ffe08a;transition:width .25s;}
  .bar-row .val{width:88px;text-align:right;font-variant-numeric:tabular-nums;}
  .note{font-size:11px;color:#9db3a6;margin-top:8px;line-height:1.5;}
  .sum .note{color:rgba(255,255,255,.7);}
  .mini{font-size:11px;color:#b08c3d;background:#fdf6e3;border-radius:8px;padding:5px 9px;margin-top:4px;}
  .warn{color:#c92a2a;font-weight:700;}
  .ok{color:#2f9e44;font-weight:700;}
</style>
</head>
<body>
<div class="wrap">
  <div class="hd">
    <div>
      <h1>农业面源污染负荷 · 互动计算器</h1>
      <div class="sub">耕地面积固定 · 作物结构/施肥量可调 · 输入即变</div>
    </div>
    <div class="seg" id="seg">
      <button class="on" data-p="TN">总氮 TN</button>
      <button data-p="TP">总磷 TP</button>
      <button data-p="COD">COD</button>
    </div>
  </div>

  <div class="card">
    <h2>⓪ 区域与基础设定</h2>
    <div class="row2">
      <div class="field"><label>区域（普查分区）</label>
        <select id="region" style="text-align:left;">
          <option value="huabei" selected>华北区</option>
          <option value="dongbei">东北区</option>
          <option value="xibei">西北区</option>
          <option value="huadong">华东区</option>
          <option value="zhongnan">中南区</option>
          <option value="xinan">西南区</option>
        </select>
      </div>
      <div class="field"><label>耕地总面积（亩）——固定</label><input id="totalArea" type="number" min="0" step="100" value="10000"></div>
      <div class="field"><label>农村人口（人）</label><input data-m="life" data-k="POP" type="number" step="100" value="5000"></div>
      <div class="field"><label>入河系数 λ（0~1）</label><input data-m="life" data-k="LAM" type="number" step="0.01" min="0" max="1" value="0.20"></div>
    </div>
    <div class="unit" style="margin-top:8px;">已分配作物面积合计 = <b id="usedArea">0</b> 亩　|　剩余 = <b id="leftArea">10000</b> 亩　|　<span id="areaMsg"></span></div>
  </div>

  <div class="card">
    <h2>① 种植业负荷（面积 × 施肥强度联动） <span class="res" id="r-crop">0 t/a</span></h2>
    <div class="formula">L = Σ( A<sub>i</sub> × Fert<sub>i</sub> × β<sub>i</sub> × Runoff<sub>i</sub> )　　A<sub>i</sub>=作物面积(亩)，Fert=施肥量(kg/亩)，β=养分含量，Runoff=流失率；超标自动标红</div>
    <table id="t-crop">
      <thead><tr>
        <th>作物</th><th>种植面积（亩）</th><th>占比%</th>
        <th>氮肥用量（kg/亩）</th><th>钾肥用量（kg/亩）</th>
        <th>氮流失率%</th><th>污染量（kg）</th><th>状态</th>
      </tr></thead>
      <tbody></tbody>
    </table>
    <div class="row2" style="margin-top:10px;">
      <div class="field"><label>修正系数 F（入河，0~1）</label><input data-m="cropF" type="number" step="0.01" min="0" max="1" value="0.10"></div>
      <div class="field"><label>&nbsp;</label><div class="unit" style="padding-top:8px;">流失量 × F = 入水体负荷</div></div>
    </div>
    <div class="unit" style="margin-top:6px;">施肥强度参考上限：水稻 N≤15、小麦 N≤18、玉米 N≤20、蔬菜 N≤30 kg/亩（示例阈值，可按当地标准改）</div>
  </div>

  <div class="card">
    <h2>② RUSLE 土壤侵蚀方程 <span class="res" id="r-rusle">0 t/a</span></h2>
    <div class="formula">A = R × K × LS × C × P　　A=侵蚀模数(t/hm²·a)；R降雨侵蚀力，K土壤可蚀性，LS坡长坡度，C植被覆盖，P水保措施</div>
    <div class="row2">
      <div class="field"><label>R 降雨侵蚀力</label><input data-m="rusle" data-k="R" type="number" step="10" value="4000"></div>
      <div class="field"><label>K 土壤可蚀性</label><input data-m="rusle" data-k="K" type="number" step="0.001" value="0.030"></div>
      <div class="field"><label>LS 坡长坡度因子</label><input data-m="rusle" data-k="LS" type="number" step="0.1" value="1.50"></div>
      <div class="field"><label>C 植被覆盖因子</label><input data-m="rusle" data-k="C" type="number" step="0.05" min="0" max="1" value="0.30"></div>
      <div class="field"><label>P 水保措施因子</label><input data-m="rusle" data-k="P" type="number" step="0.05" min="0" max="1" value="0.50"></div>
      <div class="field"><label>侵蚀面积（hm²）</label><input data-m="rusle" data-k="AREA" type="number" step="10" value="1000"></div>
      <div class="field"><label>泥沙入河比例（0~1）</label><input data-m="rusle" data-k="SDR" type="number" step="0.01" min="0" max="1" value="0.10"></div>
      <div class="field"><label>泥沙含氮（g/kg，仅TN计入汇总）</label><input data-m="rusle" data-k="NIN" type="number" step="0.1" value="0.8"></div>
    </div>
    <div class="unit" style="margin-top:8px;">侵蚀模数 A = <b id="r-mod">0</b> t/hm²·a　|　入河泥沙 = <b id="r-sed">0</b> t/a</div>
  </div>

  <div class="card">
    <h2>③ 畜禽养殖排放负荷 <span class="res" id="r-ls">0 t/a</span></h2>
    <div class="formula">L = Σ( N<sub>i</sub> × E<sub>i</sub> ) × α　　N<sub>i</sub>=存栏/出栏量，E<sub>i</sub>=产污系数(kg/头·a)，α=流失系数</div>
    <table id="t-ls"><thead><tr><th>畜禽</th><th>数量（头/只）</th><th>产污系数 E<sub>i</sub>（kg/头·a）</th><th></th></tr></thead><tbody></tbody></table>
    <div class="row2" style="margin-top:10px;">
      <div class="field"><label>流失系数 α（0~1）</label><input data-m="lsF" type="number" step="0.01" min="0" max="1" value="0.40"></div>
      <div class="field"><label>&nbsp;</label><div class="unit" style="padding-top:8px;">粪便/污水入环境水体比例</div></div>
    </div>
  </div>

  <div class="card">
    <h2>④ 渔业养殖尾水负荷 <span class="res" id="r-fish">0 t/a</span></h2>
    <div class="formula">L = Σ( M<sub>i</sub> × E<sub>i</sub> ) / 10⁶　　M<sub>i</sub>=养殖产量(kg/a)，E<sub>i</sub>=排污系数(g/kg)</div>
    <table id="t-fish"><thead><tr><th>品种</th><th>产量（kg/a）</th><th>排污系数 E<sub>i</sub>（g/kg）</th><th></th></tr></thead><tbody></tbody></table>
  </div>

  <div class="card">
    <h2>⑤ 农村生活污染负荷 <span class="res" id="r-life">0 t/a</span></h2>
    <div class="formula">L = 人口 × e × 365 × λ / 10⁶　　e=人均产污系数(g/人·d)</div>
    <div class="row2">
      <div class="field"><label>人均产污系数 e（g/人·d）</label><input data-m="life" data-k="E" type="number" step="0.5" value="12"></div>
      <div class="field"><label>&nbsp;</label><div class="unit" style="padding-top:8px;">λ 在⓪中设置</div></div>
    </div>
  </div>

  <div class="card sum">
    <h2>污染负荷汇总 <span class="res" style="color:#ffe08a;" id="r-total2"></span></h2>
    <div class="total"><span id="r-total">0</span> <small>t/a</small></div>
    <div id="bars"></div>
    <div class="note">默认系数为示例量级，正式评价请替换为《第一次/第二次全国污染源普查产排污系数手册》及当地监测值。</div>
  </div>

  <div class="mini">互动点：改作物面积→占比与剩余耕地实时变；改施肥量→污染量与"是否超标"即时刷新；切TN/TP/COD→全表系数联动。</div>
</div>

<script>
(function(){
  var LS_TN = {
    huabei:  {生猪:8,  奶牛:85, 肉牛:30, 羊:4.5, 蛋鸡:0.45, 肉鸡:0.40, 鸭:0.60},
    dongbei: {生猪:7.5,奶牛:95, 肉牛:32, 羊:5.0, 蛋鸡:0.40, 肉鸡:0.40, 鸭:0.55},
    xibei:   {生猪:7,  奶牛:90, 肉牛:28, 羊:4.0, 蛋鸡:0.45, 肉鸡:0.40, 鸭:0.60},
    huadong: {生猪:8.5,奶牛:80, 肉牛:30, 羊:4.5, 蛋鸡:0.50, 肉鸡:0.45, 鸭:0.65},
    zhongnan:{生猪:9,  奶牛:75, 肉牛:28, 羊:4.5, 蛋鸡:0.50, 肉鸡:0.45, 鸭:0.65},
    xinan:   {生猪:8,  奶牛:70, 肉牛:30, 羊:4.0, 蛋鸡:0.45, 肉鸡:0.40, 鸭:0.60}
  };
  var CROP = {
    list:['水稻','小麦','玉米','蔬菜','果园','其他'],
    defArea:{水稻:3000,小麦:2500,玉米:3000,蔬菜:1000,果园:300,其他:200},
    fertN:{水稻:15,小麦:18,玉米:20,蔬菜:30,果园:12,其他:8},
    fertK:{水稻:8,小麦:6,玉米:8,蔬菜:15,果园:10,其他:4},
    runoff:{水稻:0.12,小麦:0.10,玉米:0.11,蔬菜:0.22,果园:0.08,其他:0.06},
    betaN:0.46, betaP:0.50, codPerKgN:7.5
  };
  var FISH = {
    TN:  {草鱼:8,鲤鱼:9,鲫鱼:8,鲢鱼:7,鳙鱼:7,青鱼:8,罗非鱼:10,其他:8},
    TP:  {草鱼:1.6,鲤鱼:1.8,鲫鱼:1.6,鲢鱼:1.4,鳙鱼:1.4,青鱼:1.6,罗非鱼:2.0,其他:1.6},
    COD: {草鱼:20,鲤鱼:22,鲫鱼:20,鲢鱼:18,鳙鱼:18,青鱼:20,罗非鱼:24,其他:20}
  };
  var LIFE = {TN:12, TP:1.2, COD:60};
  var TP_RATIO=0.18, COD_RATIO=7.5;

  var cur='TN', region='huabei';
  function lsCoef(p){
    var base=LS_TN[region], out={}, f=1;
    if(p==='TP') f=TP_RATIO; else if(p==='COD') f=COD_RATIO;
    for(var k in base){ out[k]=+(base[k]*f).toFixed(3); }
    return out;
  }
  function buildCrop(){
    var tb=document.querySelector('#t-crop tbody'); tb.innerHTML='';
    CROP.list.forEach(function(k){
      var tr=document.createElement('tr');
      tr.innerHTML='<td>'+k+'</td>'+
        '<td><input data-m="crop" data-k="'+k+'" data-t="area" type="number" min="0" step="10" value="'+CROP.defArea[k]+'"></td>'+
        '<td class="unit" data-pct="'+k+'">0%</td>'+
        '<td><input data-m="crop" data-k="'+k+'" data-t="fn" type="number" min="0" step="0.5" value="'+CROP.fertN[k]+'"></td>'+
        '<td><input data-m="crop" data-k="'+k+'" data-t="fk" type="number" min="0" step="0.5" value="'+CROP.fertK[k]+'"></td>'+
        '<td><input data-m="crop" data-k="'+k+'" data-t="run" type="number" min="0" max="100" step="0.5" value="'+(CROP.runoff[k]*100)+'"></td>'+
        '<td class="unit" data-load="'+k+'">0</td>'+
        '<td class="unit" data-flag="'+k+'"></td>';
      tb.appendChild(tr);
    });
  }
  function rows(tableId, m, obj, unit){
    var tb=document.querySelector('#'+tableId+' tbody'); tb.innerHTML='';
    Object.keys(obj).forEach(function(k){
      var tr=document.createElement('tr');
      tr.innerHTML='<td>'+k+'</td>'+
        '<td><input data-m="'+m+'" data-k="'+k+'" data-t="amt" type="number" min="0" step="any" value="0"></td>'+
        '<td><input data-m="'+m+'" data-k="'+k+'" data-t="coef" type="number" min="0" step="any" value="'+obj[k]+'"></td>'+
        '<td class="unit">'+unit+'</td>';
      tb.appendChild(tr);
    });
  }
  function build(){
    rows('t-ls','ls',lsCoef(cur),'kg/头·a');
    rows('t-fish','fish',FISH[cur],'g/kg');
    document.querySelector('input[data-m="life"][data-k="E"]').value=LIFE[cur];
    recalc();
  }
  function sumTable(m, div){
    var s=0;
    document.querySelectorAll('input[data-m="'+m+'"]').forEach(function(inp){
      if(inp.dataset.t!=='amt')return;
      var amt=parseFloat(inp.value)||0;
      var coefInp=document.querySelector('input[data-m="'+m+'"][data-k="'+inp.dataset.k+'"][data-t="coef"]');
      var coef=coefInp?(parseFloat(coefInp.value)||0):0;
      s+=amt*coef;
    });
    return s/div;
  }
  function recalc(){
    var total=parseFloat(document.getElementById('totalArea').value)||0;
    var used=0, cropLoad=0;
    CROP.list.forEach(function(k){
      var area=parseFloat(document.querySelector('input[data-m="crop"][data-k="'+k+'"][data-t="area"]').value)||0;
      var fn=parseFloat(document.querySelector('input[data-m="crop"][data-k="'+k+'"][data-t="fn"]').value)||0;
      var run=(parseFloat(document.querySelector('input[data-m="crop"][data-k="'+k+'"][data-t="run"]').value)||0)/100;
      used+=area;
      var pol=area*fn*CROP.betaN*run;
      if(cur==='TP') pol=area*fn*CROP.betaP*run;
      if(cur==='COD') pol=area*fn*CROP.codPerKgN*run;
      cropLoad+=pol;
      document.querySelector('[data-pct="'+k+'"]').textContent=(total>0?(area/total*100).toFixed(1):0)+'%';
      document.querySelector('[data-load="'+k+'"]').textContent=pol.toFixed(1);
      var flag=document.querySelector('[data-flag="'+k+'"]');
      if(fn>CROP.fertN[k]){ flag.innerHTML='<span class="warn">超标</span>'; }
      else{ flag.innerHTML='<span class="ok">正常</span>'; }
    });
    document.getElementById('usedArea').textContent=used.toFixed(0);
    document.getElementById('leftArea').textContent=(total-used).toFixed(0);
    var msg=document.getElementById('areaMsg');
    if(used>total){ msg.innerHTML='<span class="warn">超出耕地面积 '+(used-total).toFixed(0)+' 亩！</span>'; }
    else if(Math.abs(used-total)<1){ msg.innerHTML='<span class="ok">面积已全部分配 ✓</span>'; }
    else{ msg.textContent='尚有 '+(total-used).toFixed(0)+' 亩未分配'; }

    var F=parseFloat(document.querySelector('input[data-m="cropF"]').value)||0;
    var crop=cropLoad*F/1000;

    var R={};
    document.querySelectorAll('input[data-m="rusle"]').forEach(function(i){R[i.dataset.k]=parseFloat(i.value)||0;});
    var mod=R.R*R.K*R.LS*R.C*R.P;
    var sed=mod*R.AREA*R.SDR;
    var rusleN=sed*(R.NIN||0)/1000;
    var ls=sumTable('ls',1000)*(parseFloat(document.querySelector('input[data-m="lsF"]').value)||0);
    var fish=sumTable('fish',1e6);
    var L={};
    document.querySelectorAll('input[data-m="life"]').forEach(function(i){L[i.dataset.k]=parseFloat(i.value)||0;});
    var life=L.POP*L.E*365*L.LAM/1e6;

    document.getElementById('r-crop').textContent=crop.toFixed(2)+' t/a';
    document.getElementById('r-mod').textContent=mod.toFixed(2);
    document.getElementById('r-sed').textContent=sed.toFixed(1);
    document.getElementById('r-rusle').textContent=(cur==='TN'?rusleN:sed).toFixed(2)+(cur==='TN'?' t/a（养分折算）':' t/a（泥沙）');
    document.getElementById('r-ls').textContent=ls.toFixed(2)+' t/a';
    document.getElementById('r-fish').textContent=fish.toFixed(2)+' t/a';
    document.getElementById('r-life').textContent=life.toFixed(2)+' t/a';

    var parts=[['种植业',crop],['土壤侵蚀',cur==='TN'?rusleN:0],['畜禽养殖',ls],['渔业养殖',fish],['农村生活',life]];
    var tot=parts.reduce(function(a,b){return a+b[1];},0);
    document.getElementById('r-total').textContent=tot.toFixed(2);
    document.getElementById('r-total2').textContent=tot.toFixed(2)+' t/a';
    var bars=document.getElementById('bars'); bars.innerHTML='';
    var colors=['#ffe08a','#ffa94d','#63e6be','#74c0fc','#e599f7'];
    parts.forEach(function(p,i){
      var pct=tot>0?(p[1]/tot*100):0;
      var d=document.createElement('div'); d.className='bar-row';
      d.innerHTML='<div class="lbl">'+p[0]+'</div><div class="track"><div class="fill" style="width:'+pct+'%;background:'+colors[i]+'"></div></div><div class="val">'+p[1].toFixed(2)+' ('+pct.toFixed(1)+'%)</div>';
      bars.appendChild(d);
    });
  }
  document.addEventListener('input',function(e){ if(e.target.tagName==='INPUT') recalc(); });
  document.getElementById('seg').addEventListener('click',function(e){
    var b=e.target.closest('button'); if(!b)return;
    document.querySelectorAll('#seg button').forEach(function(x){x.classList.remove('on');});
    b.classList.add('on'); cur=b.dataset.p; build();
  });
  document.getElementById('region').addEventListener('change',function(e){
    region=e.target.value;
    var amts={};
    document.querySelectorAll('input[data-m="ls"][data-t="amt"]').forEach(function(i){amts[i.dataset.k]=i.value;});
    rows('t-ls','ls',lsCoef(cur),'kg/头·a');
    document.querySelectorAll('input[data-m="ls"][data-t="amt"]').forEach(function(i){ if(amts[i.dataset.k]!==undefined) i.value=amts[i.dataset.k]; });
    recalc();
  });
  buildCrop();
  build();
})();
</script>
</body>
</html>
