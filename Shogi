<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>将棋盤ツール - 対局・検討用</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Noto+Serif+JP:wght@600;700&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Hiragino Kaku Gothic ProN", "Yu Gothic", sans-serif;
      background-color: #f3f4f6;
      color: #1f2937;
    }
    .shogi-font {
      font-family: 'Noto Serif JP', serif;
    }
    /* 盤面の木目とグリッド調整 */
    .shogi-board {
      background: linear-gradient(135deg, #e5b87b 0%, #dca862 50%, #d49c53 100%);
      box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.2), 0 4px 10px -2px rgba(0, 0, 0, 0.1);
      border: 2px solid #5c3a18;
    }
    .komadai {
      background: linear-gradient(135deg, #deb070 0%, #d19b54 100%);
      border: 1.5px solid #6e4720;
      box-shadow: inset 0 1px 2px rgba(255,255,255,0.3), 0 2px 4px rgba(0,0,0,0.08);
    }
    /* 将棋の駒の五角形シルエット（CSS clip-path） */
    .shogi-piece {
      clip-path: polygon(50% 4%, 88% 20%, 96% 96%, 4% 96%, 12% 20%);
      background: linear-gradient(180deg, #fdf3dc 0%, #f3dca6 100%);
      box-shadow: 0 2px 3px rgba(0, 0, 0, 0.35);
      transition: transform 0.1s ease, filter 0.1s ease;
      user-select: none;
    }
    .shogi-piece-shadow {
      filter: drop-shadow(0px 1.5px 1.5px rgba(45, 25, 5, 0.45));
    }
    .piece-gote {
      transform: rotate(180deg);
    }
    .piece-promoted {
      color: #b91c1c;
    }
    .piece-normal {
      color: #18181b;
    }
    /* 星（盤上の4つの点） */
    .star-dot {
      width: 6px;
      height: 6px;
      background-color: #3f2810;
      border-radius: 50%;
      position: absolute;
      transform: translate(-50%, -50%);
      pointer-events: none;
      z-index: 5;
    }
    /* カスタムスクロールバー */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #f1f5f9;
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb {
      background: #cbd5e1;
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #94a3b8;
    }
  </style>
</head>
<body class="min-h-screen flex flex-col bg-slate-100 text-slate-800">

  <!-- トップヘッダー：ツールバー -->
  <header class="bg-white border-b border-slate-200 px-4 py-2.5 sticky top-0 z-30 shadow-sm">
    <div class="max-w-7xl mx-auto flex flex-wrap items-center justify-between gap-3">
      <div class="flex items-center gap-2.5">
        <div class="w-8 h-8 rounded bg-amber-800 text-amber-50 flex items-center justify-center font-bold shogi-font text-lg shadow-sm">
          将
        </div>
        <div>
          <h1 class="text-base sm:text-lg font-bold text-slate-900 leading-tight">将棋盤ツール</h1>
          <p class="text-[11px] text-slate-500 hidden sm:block">対局・検討・棋譜管理ユーティリティ</p>
        </div>
      </div>

      <!-- モード・CPU設定コントロール -->
      <div class="flex flex-wrap items-center gap-2 sm:gap-3 text-xs sm:text-sm">
        <!-- 対戦モード選択 -->
        <div class="flex items-center bg-slate-100 p-1 rounded-lg border border-slate-200">
          <button id="btn-mode-pvp" onclick="setGameMode('pvp')" class="px-2.5 py-1 rounded-md font-medium transition bg-white text-slate-900 shadow-sm">
            対人戦
          </button>
          <button id="btn-mode-cpu" onclick="setGameMode('cpu')" class="px-2.5 py-1 rounded-md font-medium transition text-slate-600 hover:text-slate-900">
            CPU対戦
          </button>
        </div>

        <!-- CPU専用オプション -->
        <div id="cpu-settings" class="hidden flex flex-wrap items-center gap-2">
          <div class="flex items-center gap-1.5 bg-slate-50 px-2.5 py-1 rounded-lg border border-slate-200">
            <label for="select-cpu-level" class="text-xs font-medium text-slate-500">強さ:</label>
            <select id="select-cpu-level" onchange="onCpuSettingChange()" class="bg-transparent font-semibold text-slate-800 focus:outline-none cursor-pointer text-xs sm:text-sm">
              <option value="1">Lv.1 入門</option>
              <option value="2" selected>Lv.2 初級</option>
              <option value="3">Lv.3 中級</option>
            </select>
          </div>

          <div class="flex items-center gap-1.5 bg-slate-50 px-2.5 py-1 rounded-lg border border-slate-200">
            <label for="select-player-side" class="text-xs font-medium text-slate-500">手番:</label>
            <select id="select-player-side" onchange="onPlayerSideChange()" class="bg-transparent font-semibold text-slate-800 focus:outline-none cursor-pointer text-xs sm:text-sm">
              <option value="sente" selected>▲ 先手 (下側)</option>
              <option value="gote">△ 後手 (上側)</option>
            </select>
          </div>
        </div>

        <!-- 盤面反転ボタン -->
        <button onclick="toggleFlipBoard()" class="flex items-center gap-1.5 px-3 py-1.5 rounded-lg border border-slate-300 bg-white hover:bg-slate-50 active:bg-slate-100 text-slate-700 font-medium transition shadow-sm" title="盤面の上下を反転">
          <svg class="w-4 h-4 text-slate-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16V4m0 0L3 8m4-4l4 4m6 0v12m0 0l4-4m-4 4l-4-4"></path>
          </svg>
          <span>反転</span>
        </button>
      </div>
    </div>
  </header>

  <!-- メインコンテンツ領域 -->
  <main class="flex-1 max-w-7xl w-full mx-auto p-3 sm:p-5 flex flex-col lg:flex-row gap-5 items-center lg:items-start justify-center">

    <!-- 左側・中央：盤面および持ち駒エリア -->
    <div class="flex flex-col items-center w-full max-w-[540px]">

      <!-- ステータスバー（手番・王手・CPU思考中表示） -->
      <div class="w-full mb-2.5 px-3.5 py-2 bg-white rounded-xl border border-slate-200 shadow-sm flex items-center justify-between">
        <div class="flex items-center gap-2.5">
          <span id="turn-badge" class="inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-md text-xs sm:text-sm font-bold bg-slate-800 text-white">
            ▲ 先手番
          </span>
          <span id="move-count-label" class="text-xs sm:text-sm font-medium text-slate-500">0手目</span>
          <span id="check-badge" class="hidden px-2 py-0.5 rounded text-xs font-bold bg-red-100 text-red-700 border border-red-300 animate-pulse">
            王手
          </span>
        </div>

        <div id="status-message" class="text-xs sm:text-sm font-medium text-slate-600 truncate max-w-[200px] sm:max-w-[260px]">
          対局開始
        </div>
      </div>

      <!-- 上側プレイヤーの駒台 -->
      <div class="w-full mb-2">
        <div class="flex items-center justify-between text-xs text-slate-600 mb-1 px-1">
          <span id="top-player-label" class="font-semibold">△ 後手 持ち駒</span>
          <span id="top-player-role" class="text-[11px] text-slate-400"></span>
        </div>
        <div id="top-komadai" class="komadai w-full min-h-[46px] sm:min-h-[52px] rounded-lg px-2.5 py-1.5 flex flex-wrap items-center gap-1.5">
          <!-- JSで描画 -->
        </div>
      </div>

      <!-- 将棋盤コンテナ（筋・段の座標付き） -->
      <div class="w-full relative select-none">
        <!-- 筋番号（9〜1 または 反転時 1〜9） -->
        <div id="col-coords" class="grid grid-cols-9 pr-5 sm:pr-6 mb-0.5 text-center text-[11px] sm:text-xs font-semibold text-slate-600">
          <!-- JSで描画 -->
        </div>

        <div class="flex items-stretch">
          <!-- 9x9 盤面本体 -->
          <div class="relative flex-1 aspect-square shogi-board rounded-sm overflow-hidden">
            <!-- 星（3三, 3六, 6三, 6六の交点） -->
            <div class="star-dot" style="left: 33.333%; top: 33.333%;"></div>
            <div class="star-dot" style="left: 66.666%; top: 33.333%;"></div>
            <div class="star-dot" style="left: 33.333%; top: 66.666%;"></div>
            <div class="star-dot" style="left: 66.666%; top: 66.666%;"></div>

            <!-- 81マスのグリッド -->
            <div id="board-grid" class="w-full h-full grid grid-cols-9 grid-rows-9">
              <!-- JSで描画 -->
            </div>

            <!-- 成り・不成 選択オーバーレイモーダル -->
            <div id="promotion-modal" class="hidden absolute inset-0 bg-slate-900/50 backdrop-blur-[1px] z-20 flex items-center justify-center p-4">
              <div class="bg-white rounded-xl shadow-xl border border-slate-200 p-4 max-w-[260px] w-full text-center">
                <p class="text-sm font-bold text-slate-800 mb-3">成りますか？</p>
                <div class="grid grid-cols-2 gap-2.5">
                  <button id="btn-promote-yes" class="py-2.5 px-3 rounded-lg bg-red-600 hover:bg-red-700 text-white font-bold text-sm shadow-sm transition flex flex-col items-center gap-0.5">
                    <span id="promote-yes-kanji" class="shogi-font text-lg">龍</span>
                    <span class="text-xs font-medium">成る</span>
                  </button>
                  <button id="btn-promote-no" class="py-2.5 px-3 rounded-lg bg-slate-100 hover:bg-slate-200 text-slate-800 font-bold text-sm border border-slate-300 transition flex flex-col items-center gap-0.5">
                    <span id="promote-no-kanji" class="shogi-font text-lg">飛</span>
                    <span class="text-xs font-medium">成らない</span>
                  </button>
                </div>
              </div>
            </div>
          </div>

          <!-- 段番号（一〜九 または 反転時 九〜一） -->
          <div id="row-coords" class="w-5 sm:w-6 pl-1 grid grid-rows-9 items-center text-center text-[11px] sm:text-xs font-semibold text-slate-600">
            <!-- JSで描画 -->
          </div>
        </div>
      </div>

      <!-- 下側プレイヤーの駒台 -->
      <div class="w-full mt-2">
        <div id="bottom-komadai" class="komadai w-full min-h-[46px] sm:min-h-[52px] rounded-lg px-2.5 py-1.5 flex flex-wrap items-center gap-1.5">
          <!-- JSで描画 -->
        </div>
        <div class="flex items-center justify-between text-xs text-slate-600 mt-1 px-1">
          <span id="bottom-player-label" class="font-semibold">▲ 先手 持ち駒</span>
          <span id="bottom-player-role" class="text-[11px] text-slate-400"></span>
        </div>
      </div>

    </div>

    <!-- 右側：操作パネル＆棋譜（Kifu）ウィンドウ -->
    <div class="w-full lg:w-[340px] xl:w-[380px] flex flex-col gap-3.5">

      <!-- 検討・操作ボタン群 -->
      <div class="bg-white rounded-xl border border-slate-200 p-3.5 shadow-sm">
        <h2 class="text-xs font-bold uppercase tracking-wider text-slate-400 mb-2.5">盤面操作</h2>
        <div class="grid grid-cols-2 gap-2">
          <button id="btn-undo" onclick="undoMove()" class="flex items-center justify-center gap-1.5 py-2 px-3 rounded-lg bg-slate-100 hover:bg-slate-200 disabled:opacity-40 disabled:pointer-events-none text-slate-800 font-medium text-xs sm:text-sm transition">
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 10h10a8 8 0 018 8v2M3 10l6 6m-6-6l6-6"></path>
            </svg>
            <span>待った (1手戻る)</span>
          </button>

          <button onclick="confirmReset()" class="flex items-center justify-center gap-1.5 py-2 px-3 rounded-lg bg-slate-100 hover:bg-red-50 hover:text-red-700 hover:border-red-200 border border-transparent text-slate-700 font-medium text-xs sm:text-sm transition">
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
            </svg>
            <span>初期配置に戻す</span>
          </button>
        </div>

        <!-- リセット確認バー（alert/confirmの代替UI） -->
        <div id="reset-confirm-bar" class="hidden mt-2.5 p-2.5 bg-red-50 border border-red-200 rounded-lg flex items-center justify-between gap-2">
          <span class="text-xs font-medium text-red-800">盤面を初期化しますか？</span>
          <div class="flex items-center gap-1.5">
            <button onclick="resetGame()" class="px-2.5 py-1 rounded bg-red-600 hover:bg-red-700 text-white text-xs font-bold">実行</button>
            <button onclick="cancelReset()" class="px-2.5 py-1 rounded bg-white hover:bg-slate-100 text-slate-700 border border-slate-300 text-xs">キャンセル</button>
          </div>
        </div>
      </div>

      <!-- 棋譜ログパネル -->
      <div class="bg-white rounded-xl border border-slate-200 p-3.5 shadow-sm flex flex-col h-[340px] lg:h-[430px]">
        <div class="flex items-center justify-between mb-2 pb-2 border-b border-slate-100">
          <div class="flex items-center gap-2">
            <h2 class="text-xs font-bold uppercase tracking-wider text-slate-500">棋譜 (Kifu)</h2>
            <span id="kifu-total-badge" class="text-[11px] bg-slate-100 text-slate-600 px-1.5 py-0.5 rounded font-medium">0手</span>
          </div>
          <button onclick="copyKifuToClipboard()" class="flex items-center gap-1 px-2.5 py-1 rounded-md bg-slate-800 hover:bg-slate-700 text-white text-xs font-medium transition shadow-sm">
            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m0 0h2a2 2 0 012 2v3m2 4H10m0 0l3-3m-3 3l3 3"></path>
            </svg>
            <span id="copy-btn-text">棋譜コピー</span>
          </button>
        </div>

        <!-- 棋譜リスト表示領域 -->
        <div id="kifu-list" class="flex-1 overflow-y-auto space-y-0.5 pr-1 text-xs sm:text-sm font-mono">
          <div class="text-slate-400 text-xs py-8 text-center">まだ着手はありません</div>
        </div>

        <!-- 履歴ジャンプ操作バー -->
        <div class="mt-2 pt-2 border-t border-slate-100 grid grid-cols-4 gap-1.5">
          <button onclick="jumpToMove(0)" class="py-1.5 rounded bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold" title="開始局面へ">|&lt;</button>
          <button onclick="jumpToMove(currentHistoryIndex - 1)" class="py-1.5 rounded bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold" title="1手前へ">&lt;</button>
          <button onclick="jumpToMove(currentHistoryIndex + 1)" class="py-1.5 rounded bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold" title="1手次へ">&gt;</button>
          <button onclick="jumpToMove(historyStack.length - 1)" class="py-1.5 rounded bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold" title="最新局面へ">&gt;|</button>
        </div>
      </div>

      <!-- 簡易ルール・操作ガイド -->
      <div class="bg-slate-50 rounded-xl border border-slate-200/80 p-3 text-[11px] text-slate-500 leading-relaxed">
        <p class="font-semibold text-slate-600 mb-1">ヒント・機能</p>
        <ul class="list-disc list-inside space-y-0.5">
          <li>二歩・打ち歩詰め・行き所のない駒打ち・王手放置は自動制限されます。</li>
          <li>棋譜リストの任意の行をクリックするとその局面を確認でき、そこから指し直すことも可能です。</li>
        </ul>
      </div>

    </div>
  </main>

  <!-- トースト通知（コピー完了などのメッセージ用） -->
  <div id="toast-notification" class="fixed bottom-5 right-5 bg-slate-900 text-white text-xs sm:text-sm font-medium px-4 py-2.5 rounded-lg shadow-lg opacity-0 pointer-events-none transition-opacity duration-200 z-50">
    メッセージをここに表示
  </div>

  <script>
    /**
     * =========================================================================
     * 将棋エンジン・盤面ロジック定義
     * =========================================================================
     */

    const SENTE = 'sente';
    const GOTE = 'gote';

    // 駒の種類定義
    const PIECE_TYPES = {
      FU: 'FU', // 歩
      KY: 'KY', // 香
      KE: 'KE', // 桂
      GI: 'GI', // 銀
      KI: 'KI', // 金
      KA: 'KA', // 角
      HI: 'HI', // 飛
      OU: 'OU', // 王・玉
      TO: 'TO', // と
      NY: 'NY', // 成香
      NK: 'NK', // 成桂
      NG: 'NG', // 成銀
      UM: 'UM', // 馬
      RY: 'RY'  // 龍
    };

    // 表示用漢字マッピング
    const PIECE_KANJI = {
      FU: '歩', KY: '香', KE: '桂', GI: '銀', KI: '金', KA: '角', HI: '飛', OU: '王',
      TO: 'と', NY: '杏', NK: '圭', NG: '全', UM: '馬', RY: '龍'
    };

    // 成り駒かどうか
    const IS_PROMOTED = {
      FU: false, KY: false, KE: false, GI: false, KI: false, KA: false, HI: false, OU: false,
      TO: true,  NY: true,  NK: true,  NG: true,  UM: true,  RY: true
    };

    // 成り変換テーブル
    const PROMOTE_MAP = {
      FU: 'TO', KY: 'NY', KE: 'NK', GI: 'NG', KA: 'UM', HI: 'RY'
    };

    // 生駒への還元テーブル（持ち駒にするとき用）
    const UNPROMOTE_MAP = {
      FU: 'FU', KY: 'KY', KE: 'KE', GI: 'GI', KI: 'KI', KA: 'KA', HI: 'HI', OU: 'OU',
      TO: 'FU', NY: 'KY', NK: 'KE', NG: 'GI', UM: 'KA', RY: 'HI'
    };

    // 持ち駒の表示順
    const HAND_ORDER = ['HI', 'KA', 'KI', 'GI', 'KE', 'KY', 'FU'];

    // 座標表記用
    const ZENKAKU_NUM = ['１', '２', '３', '４', '５', '６', '７', '８', '９'];
    const KANJI_ROW   = ['一', '二', '三', '四', '五', '六', '七', '八', '九'];

    // CPU評価用の駒価値
    const PIECE_VALUES = {
      FU: 100, KY: 320, KE: 360, GI: 520, KI: 600, KA: 850, HI: 1000, OU: 15000,
      TO: 620, NY: 600, NK: 600, NG: 600, UM: 1150, RY: 1350
    };

    // 持ち駒の評価値（盤上の駒より少し高く評価して持ち駒活用・温存バランスを取る）
    const HAND_VALUES = {
      FU: 115, KY: 350, KE: 400, GI: 560, KI: 650, KA: 920, HI: 1080
    };

    /**
     * ゲーム全体の状態管理
     */
    let gameState = {
      board: [], // 9x9: row 0 = 一段目(後手陣最奥), row 8 = 九段目(先手陣最奥), col 0 = 9筋, col 8 = 1筋
      hands: {
        sente: { HI: 0, KA: 0, KI: 0, GI: 0, KE: 0, KY: 0, FU: 0 },
        gote:  { HI: 0, KA: 0, KI: 0, GI: 0, KE: 0, KY: 0, FU: 0 }
      },
      turn: SENTE,
      lastMove: null, // { fromRow, fromCol, toRow, toCol, pieceType, promoted, drop }
      kifuText: ''
    };

    // 棋譜履歴スタック（初期局面を index 0 に保持）
    let historyStack = [];
    let currentHistoryIndex = 0;

    // UI・操作状態
    let selectedCell = null; // { type: 'board', row, col } または { type: 'hand', owner, pieceType }
    let legalTargets = [];   // 選択中の駒が移動可能なマス [{ row, col, canPromote, mustPromote }]
    let pendingPromotionMove = null; // 成り選択待ちの指し手

    // モード・表示設定
    let gameMode = 'pvp';       // 'pvp' | 'cpu'
    let cpuLevel = 2;           // 1 | 2 | 3
    let playerSide = SENTE;     // CPU対戦時の人間側の手番
    let isFlipped = false;      // 盤面反転フラグ
    let isCpuThinking = false;
    let isGameOver = false;
    let cpuTimerId = null;

    /**
     * 初期盤面の生成
     * row 0: 一段目 (後手陣), row 8: 九段目 (先手陣)
     * col 0: 9筋, col 8: 1筋
     */
    function createInitialState() {
      const b = Array.from({ length: 9 }, () => Array(9).fill(null));

      // 後手 (row 0, 1, 2)
      const backRow = ['KY', 'KE', 'GI', 'KI', 'OU', 'KI', 'GI', 'KE', 'KY'];
      for (let c = 0; c < 9; c++) {
        b[0][c] = { owner: GOTE, type: backRow[c] };
        b[2][c] = { owner: GOTE, type: 'FU' };
      }
      b[1][1] = { owner: GOTE, type: 'HI' }; // 8二飛
      b[1][7] = { owner: GOTE, type: 'KA' }; // 2二角

      // 先手 (row 6, 7, 8)
      for (let c = 0; c < 9; c++) {
        b[8][c] = { owner: SENTE, type: backRow[c] };
        b[6][c] = { owner: SENTE, type: 'FU' };
      }
      b[7][7] = { owner: SENTE, type: 'HI' }; // 2八飛
      b[7][1] = { owner: SENTE, type: 'KA' }; // 8八角

      return {
        board: b,
        hands: {
          sente: { HI: 0, KA: 0, KI: 0, GI: 0, KE: 0, KY: 0, FU: 0 },
          gote:  { HI: 0, KA: 0, KI: 0, GI: 0, KE: 0, KY: 0, FU: 0 }
        },
        turn: SENTE,
        lastMove: null,
        kifuText: '開始局面'
      };
    }

    /**
     * 状態のディープコピー
     */
    function cloneState(state) {
      return {
        board: state.board.map(row => row.map(cell => cell ? { ...cell } : null)),
        hands: {
          sente: { ...state.hands.sente },
          gote:  { ...state.hands.gote }
        },
        turn: state.turn,
        lastMove: state.lastMove ? { ...state.lastMove } : null,
        kifuText: state.kifuText
      };
    }

    /**
     * =========================================================================
     * 駒の利き・移動ルール計算エンジン
     * =========================================================================
     */

    /**
     * 指定マスにある駒の「疑似合法手（王手放置チェック前）」の到達マス一覧を返す
     */
    function getPseudoAttacks(board, r, c) {
      const piece = board[r][c];
      if (!piece) return [];

      const dir = piece.owner === SENTE ? -1 : 1; // 先手は上(-1)、後手は下(+1)へ進む
      const targets = [];

      const addStep = (dr, dc) => {
        const nr = r + dr * dir;
        const nc = c + dc;
        if (nr >= 0 && nr < 9 && nc >= 0 && nc < 9) {
          const target = board[nr][nc];
          if (!target || target.owner !== piece.owner) {
            targets.push({ row: nr, col: nc });
          }
        }
      };

      const addSlide = (dr, dc) => {
        let nr = r + dr * dir;
        let nc = c + dc;
        while (nr >= 0 && nr < 9 && nc >= 0 && nc < 9) {
          const target = board[nr][nc];
          if (!target) {
            targets.push({ row: nr, col: nc });
          } else {
            if (target.owner !== piece.owner) {
              targets.push({ row: nr, col: nc });
            }
            break;
          }
          nr += dr * dir;
          nc += dc;
        }
      };

      // 金と同じ動き
      const addGoldSteps = () => {
        addStep(1, -1); addStep(1, 0); addStep(1, 1);
        addStep(0, -1);                addStep(0, 1);
                        addStep(-1, 0);
      };

      switch (piece.type) {
        case 'FU':
          addStep(1, 0);
          break;
        case 'KY':
          addSlide(1, 0);
          break;
        case 'KE':
          addStep(2, -1);
          addStep(2, 1);
          break;
        case 'GI':
          addStep(1, -1); addStep(1, 0); addStep(1, 1);
          addStep(-1, -1);               addStep(-1, 1);
          break;
        case 'KI':
        case 'TO':
        case 'NY':
        case 'NK':
        case 'NG':
          addGoldSteps();
          break;
        case 'KA':
          addSlide(1, -1); addSlide(1, 1);
          addSlide(-1, -1); addSlide(-1, 1);
          break;
        case 'HI':
          addSlide(1, 0); addSlide(-1, 0);
          addSlide(0, -1); addSlide(0, 1);
          break;
        case 'UM':
          addSlide(1, -1); addSlide(1, 1);
          addSlide(-1, -1); addSlide(-1, 1);
          addStep(1, 0); addStep(-1, 0);
          addStep(0, -1); addStep(0, 1);
          break;
        case 'RY':
          addSlide(1, 0); addSlide(-1, 0);
          addSlide(0, -1); addSlide(0, 1);
          addStep(1, -1); addStep(1, 1);
          addStep(-1, -1); addStep(-1, 1);
          break;
        case 'OU':
          addStep(1, -1); addStep(1, 0); addStep(1, 1);
          addStep(0, -1);                addStep(0, 1);
          addStep(-1, -1); addStep(-1, 0); addStep(-1, 1);
          break;
      }

      return targets;
    }

    /**
     * 指定した手番(side)の玉(OU)が王手されているか判定する
     */
    function isKingInCheck(board, side) {
      let kingRow = -1, kingCol = -1;
      for (let r = 0; r < 9; r++) {
        for (let c = 0; c < 9; c++) {
          const p = board[r][c];
          if (p && p.owner === side && p.type === 'OU') {
            kingRow = r;
            kingCol = c;
            break;
          }
        }
        if (kingRow !== -1) break;
      }
      if (kingRow === -1) return true; // 万が一玉がない場合

      const opponent = side === SENTE ? GOTE : SENTE;
      for (let r = 0; r < 9; r++) {
        for (let c = 0; c < 9; c++) {
          const p = board[r][c];
          if (p && p.owner === opponent) {
            const attacks = getPseudoAttacks(board, r, c);
            if (attacks.some(pos => pos.row === kingRow && pos.col === kingCol)) {
              return true;
            }
          }
        }
      }
      return false;
    }

    /**
     * 相手陣（成れる段）かどうか
     */
    function isPromotionZone(row, owner) {
      return owner === SENTE ? row <= 2 : row >= 6;
    }

    /**
     * 行き所のない駒になるため「強制成り」が必要か判定
     */
    function mustPromotePiece(pieceType, toRow, owner) {
      if (owner === SENTE) {
        if ((pieceType === 'FU' || pieceType === 'KY') && toRow === 0) return true;
        if (pieceType === 'KE' && toRow <= 1) return true;
      } else {
        if ((pieceType === 'FU' || pieceType === 'KY') && toRow === 8) return true;
        if (pieceType === 'KE' && toRow >= 7) return true;
      }
      return false;
    }

    /**
     * 指し手を状態に適用した新しい状態を返す（純粋関数）
     */
    function applyMoveToState(state, move) {
      const next = cloneState(state);
      const side = next.turn;
      const opponent = side === SENTE ? GOTE : SENTE;

      if (move.drop) {
        // 持ち駒を打つ
        next.hands[side][move.pieceType]--;
        next.board[move.toRow][move.toCol] = {
          owner: side,
          type: move.pieceType
        };
      } else {
        // 盤上の駒を動かす
        const movingPiece = next.board[move.fromRow][move.fromCol];
        const capturedPiece = next.board[move.toRow][move.toCol];

        if (capturedPiece) {
          const baseType = UNPROMOTE_MAP[capturedPiece.type];
          if (baseType !== 'OU') {
            next.hands[side][baseType]++;
          }
        }

        const finalType = move.promote ? PROMOTE_MAP[movingPiece.type] : movingPiece.type;
        next.board[move.toRow][move.toCol] = {
          owner: side,
          type: finalType
        };
        next.board[move.fromRow][move.fromCol] = null;
      }

      next.lastMove = { ...move };
      next.turn = opponent;
      return next;
    }

    /**
     * 指定した手番の全合法手を生成する（二歩・打ち歩詰め・行き所のない駒・王手放置を完全除外）
     */
    function generateAllLegalMoves(state, checkUchifuzume = true) {
      const side = state.turn;
      const opponent = side === SENTE ? GOTE : SENTE;
      const legalMoves = [];

      // 1. 盤上の駒の移動
      for (let r = 0; r < 9; r++) {
        for (let c = 0; c < 9; c++) {
          const piece = state.board[r][c];
          if (!piece || piece.owner !== side) continue;

          const pseudoTargets = getPseudoAttacks(state.board, r, c);
          const canEverPromote = Boolean(PROMOTE_MAP[piece.type]);

          for (const target of pseudoTargets) {
            const tr = target.row;
            const tc = target.col;
            const inZone = canEverPromote && (isPromotionZone(r, side) || isPromotionZone(tr, side));
            const forced = canEverPromote && mustPromotePiece(piece.type, tr, side);

            const candidates = [];
            if (forced) {
              candidates.push({ fromRow: r, fromCol: c, toRow: tr, toCol: tc, pieceType: piece.type, promote: true, drop: false });
            } else if (inZone) {
              candidates.push({ fromRow: r, fromCol: c, toRow: tr, toCol: tc, pieceType: piece.type, promote: true, drop: false });
              candidates.push({ fromRow: r, fromCol: c, toRow: tr, toCol: tc, pieceType: piece.type, promote: false, drop: false });
            } else {
              candidates.push({ fromRow: r, fromCol: c, toRow: tr, toCol: tc, pieceType: piece.type, promote: false, drop: false });
            }

            for (const cand of candidates) {
              const nextState = applyMoveToState(state, cand);
              if (!isKingInCheck(nextState.board, side)) {
                legalMoves.push(cand);
              }
            }
          }
        }
      }

      // 2. 持ち駒を打つ手
      // 各筋に自分の未成の歩があるか事前チェック（二歩判定用）
      const hasPawnInCol = Array(9).fill(false);
      for (let c = 0; c < 9; c++) {
        for (let r = 0; r < 9; r++) {
          const p = state.board[r][c];
          if (p && p.owner === side && p.type === 'FU') {
            hasPawnInCol[c] = true;
            break;
          }
        }
      }

      for (const pType of HAND_ORDER) {
        if (state.hands[side][pType] <= 0) continue;

        for (let r = 0; r < 9; r++) {
          // 行き所のない段への打ち込み禁止
          if (mustPromotePiece(pType, r, side)) continue;

          for (let c = 0; c < 9; c++) {
            if (state.board[r][c] !== null) continue;

            // 二歩の禁止
            if (pType === 'FU' && hasPawnInCol[c]) continue;

            const dropMove = {
              toRow: r,
              toCol: c,
              pieceType: pType,
              promote: false,
              drop: true
            };

            const nextState = applyMoveToState(state, dropMove);

            // 自玉が王手放置になっていないか
            if (isKingInCheck(nextState.board, side)) continue;

            // 打ち歩詰めの禁止チェック
            if (checkUchifuzume && pType === 'FU') {
              if (isKingInCheck(nextState.board, opponent)) {
                // 相手玉に王手がかかった場合、相手に合法手が1つもなければ打ち歩詰め反則
                const oppMoves = generateAllLegalMoves(nextState, false);
                if (oppMoves.length === 0) {
                  continue;
                }
              }
            }

            legalMoves.push(dropMove);
          }
        }
      }

      return legalMoves;
    }

    /**
     * 日本語標準棋譜表記の生成（例：▲７六歩、△２二角成、▲同　銀、▲４五歩打）
     */
    function formatKifuMove(stateBefore, move) {
      const mark = stateBefore.turn === SENTE ? '▲' : '△';
      const toSuji = ZENKAKU_NUM[8 - move.toCol];
      const toDan  = KANJI_ROW[move.toRow];
      const prevMove = stateBefore.lastMove;

      let posStr = `${toSuji}${toDan}`;
      if (prevMove && prevMove.toRow === move.toRow && prevMove.toCol === move.toCol) {
        posStr = '同　';
      }

      const pieceName = PIECE_KANJI[move.pieceType];
      let suffix = '';

      if (move.drop) {
        // 盤上の同種駒もそのマスに動ける場合のみ「打」を付けるのが正式だが、視認性のため必要に応じて付与
        let canBoardPieceReach = false;
        for (let r = 0; r < 9; r++) {
          for (let c = 0; c < 9; c++) {
            const p = stateBefore.board[r][c];
            if (p && p.owner === stateBefore.turn && p.type === move.pieceType) {
              const attacks = getPseudoAttacks(stateBefore.board, r, c);
              if (attacks.some(t => t.row === move.toRow && t.col === move.toCol)) {
                canBoardPieceReach = true;
              }
            }
          }
        }
        suffix = canBoardPieceReach ? '打' : '';
      } else {
        const canPromote = Boolean(PROMOTE_MAP[move.pieceType]) &&
                           (isPromotionZone(move.fromRow, stateBefore.turn) || isPromotionZone(move.toRow, stateBefore.turn));
        if (move.promote) {
          suffix = '成';
        } else if (canPromote && !mustPromotePiece(move.pieceType, move.toRow, stateBefore.turn)) {
          suffix = '不成';
        }
      }

      return `${mark}${posStr}${pieceName}${suffix}`;
    }

    /**
     * =========================================================================
     * CPU 思考ルーチン（Lv.1 〜 Lv.3）
     * =========================================================================
     */

    /**
     * 静的局面評価関数（先手プラス・後手マイナス）
     */
    function evaluateBoard(state) {
      let score = 0;

      for (let r = 0; r < 9; r++) {
        for (let c = 0; c < 9; c++) {
          const p = state.board[r][c];
          if (!p) continue;

          let val = PIECE_VALUES[p.type] || 0;

          // 位置ボーナス（玉の囲い意識・駒の前進ボーナス）
          if (p.type === 'OU') {
            // 玉は自陣の隅（1九や9九付近）にいる方が安全度が高い
            const homeRankDist = p.owner === SENTE ? (8 - r) : r;
            const cornerDist = Math.abs(c - 4); // 中央(5筋)から離れるほどボーナス
            val += cornerDist * 18 - homeRankDist * 25;
          } else {
            // 攻撃駒は相手陣に近づくほど価値が上がる
            const advance = p.owner === SENTE ? (8 - r) : r;
            val += advance * 6;
            // 中央制圧ボーナス
            val += (4 - Math.abs(c - 4)) * 4;
          }

          score += p.owner === SENTE ? val : -val;
        }
      }

      // 持ち駒の評価
      for (const pType of HAND_ORDER) {
        score += state.hands.sente[pType] * HAND_VALUES[pType];
        score -= state.hands.gote[pType] * HAND_VALUES[pType];
      }

      return score;
    }

    /**
     * Alpha-Beta探索
     */
    function alphaBeta(state, depth, alpha, beta, maximizingPlayer) {
      const legalMoves = generateAllLegalMoves(state, false);
      if (legalMoves.length === 0) {
        // 詰みまたはステイルメイト
        return maximizingPlayer ? -99999 - depth * 100 : 99999 + depth * 100;
      }

      if (depth === 0) {
        return evaluateBoard(state);
      }

      // 探索効率向上のためのムーブオーダリング（駒を取る手・成る手を優先）
      legalMoves.sort((a, b) => {
        const scoreA = (state.board[a.toRow][a.toCol] ? 10 : 0) + (a.promote ? 5 : 0);
        const scoreB = (state.board[b.toRow][b.toCol] ? 10 : 0) + (b.promote ? 5 : 0);
        return scoreB - scoreA;
      });

      if (maximizingPlayer) {
        let maxEval = -Infinity;
        for (const move of legalMoves) {
          const next = applyMoveToState(state, move);
          const ev = alphaBeta(next, depth - 1, alpha, beta, false);
          if (ev > maxEval) maxEval = ev;
          if (ev > alpha) alpha = ev;
          if (beta <= alpha) break;
        }
        return maxEval;
      } else {
        let minEval = Infinity;
        for (const move of legalMoves) {
          const next = applyMoveToState(state, move);
          const ev = alphaBeta(next, depth - 1, alpha, beta, true);
          if (ev < minEval) minEval = ev;
          if (ev < beta) beta = ev;
          if (beta <= alpha) break;
        }
        return minEval;
      }
    }

    /**
     * CPUの次の一手を決定する
     */
    function chooseCpuMove(state, level) {
      const legalMoves = generateAllLegalMoves(state, true);
      if (legalMoves.length === 0) return null;

      const isSente = state.turn === SENTE;

      // Lv.1 (入門): ランダム主体（駒を取れる手があれば少しだけ確率を上げる）
      if (level === 1) {
        const weighted = [];
        for (const m of legalMoves) {
          const isCapture = Boolean(state.board[m.toRow][m.toCol]);
          const weight = isCapture ? 3 : 1;
          for (let i = 0; i < weight; i++) weighted.push(m);
        }
        return weighted[Math.floor(Math.random() * weighted.length)];
      }

      // Lv.2 (初級): 1手読み（駒得・成り・王手を評価し、わずかな揺らぎを加える）
      if (level === 2) {
        let bestMoves = [];
        let bestScore = isSente ? -Infinity : Infinity;

        for (const move of legalMoves) {
          const next = applyMoveToState(state, move);
          let score = evaluateBoard(next);
          // 王手をかける手に少しボーナス
          if (isKingInCheck(next.board, next.turn)) {
            score += isSente ? 45 : -45;
          }
          // 同点時の多様性のため ±15 のノイズを加算
          score += (Math.random() * 30 - 15);

          if (isSente) {
            if (score > bestScore) { bestScore = score; bestMoves = [move]; }
          } else {
            if (score < bestScore) { bestScore = score; bestMoves = [move]; }
          }
        }
        return bestMoves[0];
      }

      // Lv.3 (中級): 2〜3手読み Alpha-Beta探索
      // 合法手数に応じて探索深さを自動調整（レスポンス速度の維持）
      const depth = legalMoves.length > 75 ? 2 : 3;
      let bestMove = legalMoves[0];
      let bestScore = isSente ? -Infinity : Infinity;
      let alpha = -Infinity;
      let beta = Infinity;

      // 駒取り・成りを優先ソート
      legalMoves.sort((a, b) => {
        const sA = (state.board[a.toRow][a.toCol] ? 10 : 0) + (a.promote ? 5 : 0);
        const sB = (state.board[b.toRow][b.toCol] ? 10 : 0) + (b.promote ? 5 : 0);
        return sB - sA;
      });

      for (const move of legalMoves) {
        const next = applyMoveToState(state, move);
        const score = alphaBeta(next, depth - 1, alpha, beta, !isSente) + (Math.random() * 6 - 3);

        if (isSente) {
          if (score > bestScore) {
            bestScore = score;
            bestMove = move;
          }
          if (score > alpha) alpha = score;
        } else {
          if (score < bestScore) {
            bestScore = score;
            bestMove = move;
          }
          if (score < beta) beta = score;
        }
      }

      return bestMove;
    }

    /**
     * =========================================================================
     * 対局進行・ユーザー操作ハンドラ
     * =========================================================================
     */

    function initApp() {
      const initial = createInitialState();
      gameState = cloneState(initial);
      historyStack = [cloneState(initial)];
      currentHistoryIndex = 0;
      selectedCell = null;
      legalTargets = [];
      isGameOver = false;
      renderAll();
    }

    /**
     * モード切替（対人 / CPU）
     */
    function setGameMode(mode) {
      gameMode = mode;
      const btnPvp = document.getElementById('btn-mode-pvp');
      const btnCpu = document.getElementById('btn-mode-cpu');
      const cpuSettings = document.getElementById('cpu-settings');

      if (mode === 'pvp') {
        btnPvp.className = 'px-2.5 py-1 rounded-md font-medium transition bg-white text-slate-900 shadow-sm';
        btnCpu.className = 'px-2.5 py-1 rounded-md font-medium transition text-slate-600 hover:text-slate-900';
        cpuSettings.classList.add('hidden');
      } else {
        btnCpu.className = 'px-2.5 py-1 rounded-md font-medium transition bg-white text-slate-900 shadow-sm';
        btnPvp.className = 'px-2.5 py-1 rounded-md font-medium transition text-slate-600 hover:text-slate-900';
        cpuSettings.classList.remove('hidden');
      }

      clearSelection();
      updatePlayerLabels();
      triggerCpuIfNeeded();
    }

    function onCpuSettingChange() {
      cpuLevel = parseInt(document.getElementById('select-cpu-level').value, 10);
      updatePlayerLabels();
    }

    function onPlayerSideChange() {
      playerSide = document.getElementById('select-player-side').value;
      // プレイヤーが後手を選んだ場合は自動的に盤面を後手視点に反転
      isFlipped = (playerSide === GOTE);
      clearSelection();
      renderAll();
      triggerCpuIfNeeded();
    }

    function toggleFlipBoard() {
      isFlipped = !isFlipped;
      renderAll();
    }

    function clearSelection() {
      selectedCell = null;
      legalTargets = [];
      pendingPromotionMove = null;
      document.getElementById('promotion-modal').classList.add('hidden');
    }

    /**
     * 盤面のマスをクリックした時の処理
     */
    function onBoardCellClick(row, col) {
      if (isCpuThinking || isGameOver) return;
      if (gameMode === 'cpu' && gameState.turn !== playerSide) return;

      // 移動先候補としてハイライトされているマスをクリックした場合
      const targetMatch = legalTargets.find(t => t.row === row && t.col === col);
      if (selectedCell && targetMatch) {
        if (selectedCell.type === 'hand') {
          executeMove({
            toRow: row,
            toCol: col,
            pieceType: selectedCell.pieceType,
            promote: false,
            drop: true
          });
          return;
        } else if (selectedCell.type === 'board') {
          const baseMove = {
            fromRow: selectedCell.row,
            fromCol: selectedCell.col,
            toRow: row,
            toCol: col,
            pieceType: gameState.board[selectedCell.row][selectedCell.col].type,
            drop: false
          };

          if (targetMatch.mustPromote) {
            executeMove({ ...baseMove, promote: true });
          } else if (targetMatch.canPromote) {
            // 成り・不成の選択ダイアログを表示
            pendingPromotionMove = baseMove;
            const modal = document.getElementById('promotion-modal');
            document.getElementById('promote-yes-kanji').textContent = PIECE_KANJI[PROMOTE_MAP[baseMove.pieceType]];
            document.getElementById('promote-no-kanji').textContent = PIECE_KANJI[baseMove.pieceType];
            modal.classList.remove('hidden');
          } else {
            executeMove({ ...baseMove, promote: false });
          }
          return;
        }
      }

      // 自分の盤上の駒を選択した場合
      const clickedPiece = gameState.board[row][col];
      if (clickedPiece && clickedPiece.owner === gameState.turn) {
        // すでに選択中の駒を再クリックした場合は解除
        if (selectedCell && selectedCell.type === 'board' && selectedCell.row === row && selectedCell.col === col) {
          clearSelection();
          renderBoard();
          renderHands();
          return;
        }

        selectedCell = { type: 'board', row, col };
        computeLegalTargetsForSelection();
        renderBoard();
        renderHands();
        return;
      }

      // それ以外の空マスや相手駒をクリックした場合は選択解除
      clearSelection();
      renderBoard();
      renderHands();
    }

    /**
     * 持ち駒をクリックした時の処理
     */
    function onHandPieceClick(owner, pieceType) {
      if (isCpuThinking || isGameOver) return;
      if (owner !== gameState.turn) return;
      if (gameMode === 'cpu' && gameState.turn !== playerSide) return;
      if (gameState.hands[owner][pieceType] <= 0) return;

      if (selectedCell && selectedCell.type === 'hand' && selectedCell.pieceType === pieceType) {
        clearSelection();
        renderBoard();
        renderHands();
        return;
      }

      selectedCell = { type: 'hand', owner, pieceType };
      computeLegalTargetsForSelection();
      renderBoard();
      renderHands();
    }

    /**
     * 選択中の駒に対する合法移動先を抽出
     */
    function computeLegalTargetsForSelection() {
      legalTargets = [];
      if (!selectedCell) return;

      const allMoves = generateAllLegalMoves(gameState, true);

      if (selectedCell.type === 'board') {
        const filtered = allMoves.filter(m => !m.drop && m.fromRow === selectedCell.row && m.fromCol === selectedCell.col);
        const map = new Map();
        for (const m of filtered) {
          const key = `${m.toRow},${m.toCol}`;
          if (!map.has(key)) {
            map.set(key, { row: m.toRow, col: m.toCol, canPromote: false, mustPromote: false });
          }
          const entry = map.get(key);
          if (m.promote) entry.canPromote = true;
        }
        // promote: true のみ存在し promote: false が存在しない場合は強制成り
        for (const [key, entry] of map.entries()) {
          const hasUnpromoted = filtered.some(m => m.toRow === entry.row && m.toCol === entry.col && !m.promote);
          if (entry.canPromote && !hasUnpromoted) {
            entry.mustPromote = true;
          }
          legalTargets.push(entry);
        }
      } else if (selectedCell.type === 'hand') {
        const filtered = allMoves.filter(m => m.drop && m.pieceType === selectedCell.pieceType);
        for (const m of filtered) {
          legalTargets.push({ row: m.toRow, col: m.toCol, canPromote: false, mustPromote: false });
        }
      }
    }

    // 成りモーダルのボタンイベント登録
    document.getElementById('btn-promote-yes').addEventListener('click', () => {
      if (pendingPromotionMove) {
        const move = { ...pendingPromotionMove, promote: true };
        clearSelection();
        executeMove(move);
      }
    });

    document.getElementById('btn-promote-no').addEventListener('click', () => {
      if (pendingPromotionMove) {
        const move = { ...pendingPromotionMove, promote: false };
        clearSelection();
        executeMove(move);
      }
    });

    /**
     * 指し手を確定して局面を進める
     */
    function executeMove(move) {
      const kifuStr = formatKifuMove(gameState, move);
      const nextState = applyMoveToState(gameState, move);
      nextState.kifuText = kifuStr;

      // 過去局面を閲覧中に新しい手を指した場合は、それ以降の分岐履歴を切り捨てる
      if (currentHistoryIndex < historyStack.length - 1) {
        historyStack = historyStack.slice(0, currentHistoryIndex + 1);
      }

      historyStack.push(cloneState(nextState));
      currentHistoryIndex = historyStack.length - 1;
      gameState = cloneState(nextState);

      clearSelection();
      checkGameStatusAndRender();
      triggerCpuIfNeeded();
    }

    /**
     * 詰み・王手判定を行い画面全体を更新
     */
    function checkGameStatusAndRender() {
      const inCheck = isKingInCheck(gameState.board, gameState.turn);
      const legalMoves = generateAllLegalMoves(gameState, true);

      const checkBadge = document.getElementById('check-badge');
      const statusMsg = document.getElementById('status-message');

      if (legalMoves.length === 0) {
        isGameOver = true;
        const winner = gameState.turn === SENTE ? '後手' : '先手';
        if (inCheck) {
          checkBadge.classList.remove('hidden');
          statusMsg.textContent = `詰み — ${winner}の勝ち`;
          statusMsg.className = 'text-xs sm:text-sm font-bold text-red-600';
        } else {
          checkBadge.classList.add('hidden');
          statusMsg.textContent = `ステイルメイト — ${winner}の勝ち`;
          statusMsg.className = 'text-xs sm:text-sm font-bold text-amber-700';
        }
      } else {
        isGameOver = false;
        if (inCheck) {
          checkBadge.classList.remove('hidden');
          statusMsg.textContent = '王手がかかっています';
          statusMsg.className = 'text-xs sm:text-sm font-semibold text-red-600';
        } else {
          checkBadge.classList.add('hidden');
          statusMsg.textContent = currentHistoryIndex === 0 ? '対局開始' : `${currentHistoryIndex}手目: ${gameState.kifuText}`;
          statusMsg.className = 'text-xs sm:text-sm font-medium text-slate-600 truncate max-w-[200px] sm:max-w-[260px]';
        }
      }

      renderAll();
    }

    /**
     * CPUの手番であれば非同期で思考・着手を実行
     */
    function triggerCpuIfNeeded() {
      if (cpuTimerId) {
        clearTimeout(cpuTimerId);
        cpuTimerId = null;
      }

      if (gameMode !== 'cpu' || isGameOver) {
        isCpuThinking = false;
        return;
      }

      // 最新局面でのみCPUは思考する（過去棋譜閲覧中は動かさない）
      if (currentHistoryIndex !== historyStack.length - 1) {
        isCpuThinking = false;
        return;
      }

      if (gameState.turn !== playerSide) {
        isCpuThinking = true;
        const statusMsg = document.getElementById('status-message');
        statusMsg.textContent = `CPU (Lv.${cpuLevel}) 思考中...`;
        statusMsg.className = 'text-xs sm:text-sm font-semibold text-indigo-600 animate-pulse';

        cpuTimerId = setTimeout(() => {
          const cpuMove = chooseCpuMove(gameState, cpuLevel);
          isCpuThinking = false;
          if (cpuMove) {
            executeMove(cpuMove);
          }
        }, 280);
      } else {
        isCpuThinking = false;
      }
    }

    /**
     * 待った（1手戻る / CPU戦の場合は人間の手番まで戻す）
     */
    function undoMove() {
      if (currentHistoryIndex <= 0) return;
      if (cpuTimerId) {
        clearTimeout(cpuTimerId);
        cpuTimerId = null;
      }
      isCpuThinking = false;
      clearSelection();

      let targetIndex = currentHistoryIndex - 1;
      // CPU対戦で現在が人間の手番なら、CPUの手と自分の手の2手分戻す
      if (gameMode === 'cpu' && gameState.turn === playerSide && targetIndex >= 1) {
        targetIndex -= 1;
      }

      historyStack = historyStack.slice(0, targetIndex + 1);
      currentHistoryIndex = targetIndex;
      gameState = cloneState(historyStack[currentHistoryIndex]);
      checkGameStatusAndRender();
    }

    /**
     * 任意の棋譜インデックスへジャンプ（検討用）
     */
    function jumpToMove(index) {
      if (index < 0 || index >= historyStack.length) return;
      if (cpuTimerId) {
        clearTimeout(cpuTimerId);
        cpuTimerId = null;
      }
      isCpuThinking = false;
      clearSelection();

      currentHistoryIndex = index;
      gameState = cloneState(historyStack[currentHistoryIndex]);
      checkGameStatusAndRender();
    }

    /**
     * 盤面リセット確認・実行
     */
    function confirmReset() {
      document.getElementById('reset-confirm-bar').classList.remove('hidden');
    }

    function cancelReset() {
      document.getElementById('reset-confirm-bar').classList.add('hidden');
    }

    function resetGame() {
      cancelReset();
      if (cpuTimerId) {
        clearTimeout(cpuTimerId);
        cpuTimerId = null;
      }
      isCpuThinking = false;
      initApp();
      triggerCpuIfNeeded();
      showToast('初期配置に戻しました');
    }

    /**
     * 棋譜テキストのクリップボードコピー
     */
    function copyKifuToClipboard() {
      if (historyStack.length <= 1) {
        showToast('コピーする棋譜がありません');
        return;
      }

      const lines = [];
      lines.push(`# 将棋盤ツール 棋譜データ`);
      lines.push(`手数：${historyStack.length - 1}手`);
      lines.push('---');
      for (let i = 1; i < historyStack.length; i++) {
        lines.push(`${String(i).padStart(3, ' ')} ${historyStack[i].kifuText}`);
      }

      const text = lines.join('\n');
      const textarea = document.createElement('textarea');
      textarea.value = text;
      textarea.style.position = 'fixed';
      textarea.style.opacity = '0';
      document.body.appendChild(textarea);
      textarea.select();
      try {
        document.execCommand('copy');
        showToast('棋譜をクリップボードにコピーしました');
      } catch (err) {
        showToast('コピーに失敗しました');
      }
      document.body.removeChild(textarea);
    }

    function showToast(msg) {
      const toast = document.getElementById('toast-notification');
      toast.textContent = msg;
      toast.classList.remove('opacity-0');
      setTimeout(() => {
        toast.classList.add('opacity-0');
      }, 2200);
    }

    /**
     * =========================================================================
     * UI レンダリング処理
     * =========================================================================
     */

    function renderAll() {
      updateStatusHeader();
      updatePlayerLabels();
      renderCoordinates();
      renderBoard();
      renderHands();
      renderKifuList();
    }

    function updateStatusHeader() {
      const turnBadge = document.getElementById('turn-badge');
      const moveCountLabel = document.getElementById('move-count-label');
      const btnUndo = document.getElementById('btn-undo');

      if (gameState.turn === SENTE) {
        turnBadge.textContent = '▲ 先手番';
        turnBadge.className = 'inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-md text-xs sm:text-sm font-bold bg-slate-800 text-white';
      } else {
        turnBadge.textContent = '△ 後手番';
        turnBadge.className = 'inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-md text-xs sm:text-sm font-bold bg-white text-slate-800 border border-slate-400';
      }

      moveCountLabel.textContent = `${currentHistoryIndex}手目`;
      btnUndo.disabled = currentHistoryIndex <= 0;
    }

    function updatePlayerLabels() {
      const topSide = isFlipped ? SENTE : GOTE;
      const bottomSide = isFlipped ? GOTE : SENTE;

      document.getElementById('top-player-label').textContent = topSide === SENTE ? '▲ 先手 持ち駒' : '△ 後手 持ち駒';
      document.getElementById('bottom-player-label').textContent = bottomSide === SENTE ? '▲ 先手 持ち駒' : '△ 後手 持ち駒';

      if (gameMode === 'cpu') {
        document.getElementById('top-player-role').textContent = topSide === playerSide ? 'あなた' : `CPU (Lv.${cpuLevel})`;
        document.getElementById('bottom-player-role').textContent = bottomSide === playerSide ? 'あなた' : `CPU (Lv.${cpuLevel})`;
      } else {
        document.getElementById('top-player-role').textContent = '';
        document.getElementById('bottom-player-role').textContent = '';
      }
    }

    function renderCoordinates() {
      const colCoords = document.getElementById('col-coords');
      const rowCoords = document.getElementById('row-coords');

      colCoords.innerHTML = '';
      rowCoords.innerHTML = '';

      for (let i = 0; i < 9; i++) {
        // 通常時は左から 9,8,7...1、反転時は 1,2,3...9
        const sujiNum = isFlipped ? (i + 1) : (9 - i);
        const colDiv = document.createElement('div');
        colDiv.textContent = sujiNum;
        colCoords.appendChild(colDiv);

        // 通常時は上から 一,二...九、反転時は 九,八...一
        const danIdx = isFlipped ? (8 - i) : i;
        const rowDiv = document.createElement('div');
        rowDiv.textContent = KANJI_ROW[danIdx];
        rowCoords.appendChild(rowDiv);
      }
    }

    function renderBoard() {
      const grid = document.getElementById('board-grid');
      grid.innerHTML = '';

      for (let viewR = 0; viewR < 9; viewR++) {
        for (let viewC = 0; viewC < 9; viewC++) {
          const r = isFlipped ? (8 - viewR) : viewR;
          const c = isFlipped ? (8 - viewC) : viewC;

          const cell = document.createElement('div');
          const isSelected = selectedCell && selectedCell.type === 'board' && selectedCell.row === r && selectedCell.col === c;
          const targetInfo = legalTargets.find(t => t.row === r && t.col === c);
          const isLastTo = gameState.lastMove && gameState.lastMove.toRow === r && gameState.lastMove.toCol === c;
          const isLastFrom = gameState.lastMove && !gameState.lastMove.drop && gameState.lastMove.fromRow === r && gameState.lastMove.fromCol === c;

          let bgClass = '';
          if (isSelected) {
            bgClass = 'bg-amber-300/80';
          } else if (isLastTo) {
            bgClass = 'bg-yellow-200/65';
          } else if (isLastFrom) {
            bgClass = 'bg-yellow-100/40';
          } else {
            bgClass = 'hover:bg-amber-900/10';
          }

          cell.className = `relative border-[0.5px] border-[#6b441c]/70 flex items-center justify-center cursor-pointer transition-colors ${bgClass}`;
          cell.onclick = () => onBoardCellClick(r, c);

          const piece = gameState.board[r][c];
          if (piece) {
            // 視覚的な向き：通常の先手駒は上向き、後手駒は180度回転。盤面反転時は逆になる。
            const isUpsideDown = isFlipped ? (piece.owner === SENTE) : (piece.owner === GOTE);
            const isProm = IS_PROMOTED[piece.type];

            // 玉の表示分け（先手は王、後手は玉）
            let kanji = PIECE_KANJI[piece.type];
            if (piece.type === 'OU' && piece.owner === GOTE) {
              kanji = '玉';
            }

            const wrapper = document.createElement('div');
            wrapper.className = `w-[84%] h-[88%] flex items-center justify-center shogi-piece-shadow ${isUpsideDown ? 'piece-gote' : ''}`;

            const pieceEl = document.createElement('div');
            pieceEl.className = `w-full h-full shogi-piece flex items-center justify-center shogi-font font-bold text-base sm:text-xl md:text-2xl leading-none pt-0.5 ${isProm ? 'piece-promoted' : 'piece-normal'}`;
            pieceEl.textContent = kanji;

            wrapper.appendChild(pieceEl);
            cell.appendChild(wrapper);
          }

          // 合法手ハイライトマーカー
          if (targetInfo) {
            const marker = document.createElement('div');
            if (piece) {
              // 相手の駒を取れるマスは赤みがかった枠リング
              marker.className = 'absolute inset-0.5 border-2 border-red-600/80 bg-red-500/15 rounded-sm pointer-events-none';
            } else {
              // 空きマスへの移動候補は中央のドット
              marker.className = 'w-3 h-3 sm:w-3.5 sm:h-3.5 rounded-full bg-emerald-700/60 pointer-events-none shadow-sm';
            }
            cell.appendChild(marker);
          }

          grid.appendChild(cell);
        }
      }
    }

    function renderHands() {
      const topSide = isFlipped ? SENTE : GOTE;
      const bottomSide = isFlipped ? GOTE : SENTE;

      renderSingleHand('top-komadai', topSide);
      renderSingleHand('bottom-komadai', bottomSide);
    }

    function renderSingleHand(containerId, owner) {
      const container = document.getElementById(containerId);
      container.innerHTML = '';

      const hand = gameState.hands[owner];
      let hasAny = false;

      for (const pType of HAND_ORDER) {
        const count = hand[pType];
        if (count <= 0) continue;
        hasAny = true;

        const isSelected = selectedCell && selectedCell.type === 'hand' && selectedCell.owner === owner && selectedCell.pieceType === pType;
        const canSelect = (owner === gameState.turn) && !isGameOver && !(gameMode === 'cpu' && gameState.turn !== playerSide);

        const btn = document.createElement('button');
        btn.type = 'button';
        btn.className = `relative flex items-center gap-1 px-2 py-1 rounded-md transition ${
          isSelected
            ? 'bg-amber-200 ring-2 ring-amber-600 shadow-sm'
            : canSelect
              ? 'hover:bg-amber-900/15 cursor-pointer'
              : 'cursor-default opacity-95'
        }`;
        btn.onclick = () => onHandPieceClick(owner, pType);

        const pieceWrap = document.createElement('div');
        pieceWrap.className = 'w-7 h-8 sm:w-8 sm:h-9 shogi-piece-shadow flex items-center justify-center';

        const pieceEl = document.createElement('div');
        pieceEl.className = 'w-full h-full shogi-piece flex items-center justify-center shogi-font font-bold text-sm sm:text-base piece-normal pt-0.5';
        pieceEl.textContent = PIECE_KANJI[pType];

        pieceWrap.appendChild(pieceEl);
        btn.appendChild(pieceWrap);

        if (count > 1) {
          const badge = document.createElement('span');
          badge.className = 'text-xs font-bold text-amber-950 bg-amber-100/90 border border-amber-700/40 px-1.5 py-0.2 rounded-full';
          badge.textContent = `${count}`;
          btn.appendChild(badge);
        }

        container.appendChild(btn);
      }

      if (!hasAny) {
        const emptyHint = document.createElement('span');
        emptyHint.className = 'text-xs text-amber-950/50 px-1 select-none';
        emptyHint.textContent = 'なし';
        container.appendChild(emptyHint);
      }
    }

    function renderKifuList() {
      const listEl = document.getElementById('kifu-list');
      const badgeEl = document.getElementById('kifu-total-badge');
      badgeEl.textContent = `${historyStack.length - 1}手`;

      listEl.innerHTML = '';

      // 0手目（開始局面）
      const startRow = document.createElement('div');
      startRow.className = `flex items-center justify-between px-2.5 py-1.5 rounded cursor-pointer transition ${
        currentHistoryIndex === 0 ? 'bg-slate-800 text-white font-semibold' : 'hover:bg-slate-100 text-slate-600'
      }`;
      startRow.onclick = () => jumpToMove(0);
      startRow.innerHTML = `<span>0. 開始局面</span>`;
      listEl.appendChild(startRow);

      for (let i = 1; i < historyStack.length; i++) {
        const item = historyStack[i];
        const isCurrent = (i === currentHistoryIndex);
        const row = document.createElement('div');
        row.className = `flex items-center justify-between px-2.5 py-1.5 rounded cursor-pointer transition ${
          isCurrent ? 'bg-slate-800 text-white font-semibold' : 'hover:bg-slate-100 text-slate-700'
        }`;
        row.onclick = () => jumpToMove(i);

        const numSpan = document.createElement('span');
        numSpan.className = `w-10 text-xs ${isCurrent ? 'text-slate-300' : 'text-slate-400'}`;
        numSpan.textContent = `${i}.`;

        const moveSpan = document.createElement('span');
        moveSpan.className = 'flex-1 font-medium';
        moveSpan.textContent = item.kifuText;

        row.appendChild(numSpan);
        row.appendChild(moveSpan);
        listEl.appendChild(row);
      }

      // 現在の手が見える位置へ自動スクロール
      const activeEl = listEl.children[currentHistoryIndex];
      if (activeEl) {
        activeEl.scrollIntoView({ block: 'nearest' });
      }
    }

    // アプリケーション起動
    window.onload = function() {
      initApp();
    };
  </script>
</body>
</html>
