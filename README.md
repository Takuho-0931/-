import React, { useState, useEffect } from 'react';
import { Play, Volume2, Target, Trophy, Award, RotateCcw, Zap } from 'lucide-react';

// 型定義
interface GameClip {
  clip_id: string;
  meta: {
    difficulty: number;
    duration_sec: number;
    lexicon_tags: string[];
    wpm: number;
    stage: number;
  };
  questions: Question[];
  script: string;
}

interface Question {
  qid: string;
  prompt: string;
  choices: string[];
  answer_index: number;
  rationale: string;
}

interface StageInfo {
  id: number;
  name: string;
  description: string;
  theme: string;
  color: string;
  minScore: number;
}

interface PlayerStats {
  totalScore: number;
  streak: number;
  averageResponseTime: number;
  currentStage: number;
  questionsCompleted: number;
}

// ステージデータ
const STAGES: StageInfo[] = [
  {
    id: 1,
    name: "ROOKIE LEAGUE",
    description: "基本の英検5級語彙をマスター",
    theme: "Training Ground",
    color: "from-green-500 to-blue-500",
    minScore: 0
  },
  {
    id: 2,
    name: "PRO LEAGUE", 
    description: "英検4級レベルで実力アップ",
    theme: "Stadium Match",
    color: "from-blue-500 to-purple-500",
    minScore: 400
  },
  {
    id: 3,
    name: "CHAMPIONS LEAGUE",
    description: "英検3級の高度な語彙に挑戦", 
    theme: "World Cup Final",
    color: "from-purple-500 to-pink-500",
    minScore: 700
  },
  {
    id: 4,
    name: "LEGENDARY MASTER",
    description: "英検準2級の究極レベル",
    theme: "Olympic Stadium", 
    color: "from-yellow-500 to-red-500",
    minScore: 1000
  }
];

// クリップデータ
const SAMPLE_CLIPS: GameClip[] = [
  // Stage 1
  {
    clip_id: "stage1_001",
    meta: { difficulty: 1, duration_sec: 4, lexicon_tags: ["good", "英検5級"], wpm: 80, stage: 1 },
    questions: [{
      qid: "q1", prompt: "選手の評価は？",
      choices: ["😊 good（良い）", "😢 bad（悪い）", "😐 okay（普通）"],
      answer_index: 0, rationale: "「Good player!」のgoodは「良い」という意味です！"
    }],
    script: "Good player! Nice goal!"
  },
  {
    clip_id: "stage1_002",
    meta: { difficulty: 1, duration_sec: 5, lexicon_tags: ["fast", "英検5級"], wpm: 75, stage: 1 },
    questions: [{
      qid: "q1", prompt: "選手の走り方は？",
      choices: ["🏃‍♂️ fast（速い）", "🚶‍♂️ slow（遅い）", "🛑 stop（止まる）"],
      answer_index: 0, rationale: "「He runs fast!」のfastは「速い」です！"
    }],
    script: "He runs fast! Amazing speed!"
  },
  {
    clip_id: "stage1_003",
    meta: { difficulty: 1, duration_sec: 4, lexicon_tags: ["happy", "英検5級"], wpm: 80, stage: 1 },
    questions: [{
      qid: "q1", prompt: "チームの気持ちは？",
      choices: ["😄 happy（嬉しい）", "😢 sad（悲しい）", "😠 angry（怒り）"],
      answer_index: 0, rationale: "「The team is happy!」のhappyは「嬉しい」！"
    }],
    script: "Goal! The team is very happy!"
  },
  {
    clip_id: "stage1_004",
    meta: { difficulty: 1, duration_sec: 5, lexicon_tags: ["big", "英検5級"], wpm: 75, stage: 1 },
    questions: [{
      qid: "q1", prompt: "キックの大きさは？",
      choices: ["🦶 big（大きい）", "🤏 small（小さい）", "📏 long（長い）"],
      answer_index: 0, rationale: "「Big kick!」のbigは「大きい」という意味！"
    }],
    script: "What a big kick!"
  },
  {
    clip_id: "stage1_005",
    meta: { difficulty: 1, duration_sec: 4, lexicon_tags: ["new", "英検5級"], wpm: 80, stage: 1 },
    questions: [{
      qid: "q1", prompt: "選手について",
      choices: ["✨ new（新しい）", "👴 old（古い）", "🔄 same（同じ）"],
      answer_index: 0, rationale: "「New player!」のnewは「新しい」です！"
    }],
    script: "Here comes a new player!"
  },
  // Stage 2
  {
    clip_id: "stage2_001",
    meta: { difficulty: 2, duration_sec: 6, lexicon_tags: ["beautiful", "英検4級"], wpm: 85, stage: 2 },
    questions: [{
      qid: "q1", prompt: "ゴールの美しさは？",
      choices: ["✨ beautiful（美しい）", "😱 terrible（ひどい）", "😐 normal（普通）"],
      answer_index: 0, rationale: "「Beautiful goal!」のbeautifulは「美しい」！"
    }],
    script: "What a beautiful goal! Fantastic!"
  },
  {
    clip_id: "stage2_002",
    meta: { difficulty: 2, duration_sec: 7, lexicon_tags: ["important", "英検4級"], wpm: 90, stage: 2 },
    questions: [{
      qid: "q1", prompt: "この瞬間は？",
      choices: ["⭐ important（重要）", "🎪 funny（面白い）", "😴 boring（退屈）"],
      answer_index: 0, rationale: "「Important moment!」のimportantは「重要な」！"
    }],
    script: "This is a very important moment!"
  },
  {
    clip_id: "stage2_003",
    meta: { difficulty: 2, duration_sec: 6, lexicon_tags: ["dangerous", "英検4級"], wpm: 85, stage: 2 },
    questions: [{
      qid: "q1", prompt: "この攻撃は？",
      choices: ["⚠️ dangerous（危険）", "🛡️ safe（安全）", "😊 easy（簡単）"],
      answer_index: 0, rationale: "「Dangerous attack!」のdangerousは「危険な」！"
    }],
    script: "Dangerous attack from the red team!"
  },
  {
    clip_id: "stage2_004",
    meta: { difficulty: 2, duration_sec: 7, lexicon_tags: ["perfect", "英検4級"], wpm: 85, stage: 2 },
    questions: [{
      qid: "q1", prompt: "パスの精度は？",
      choices: ["💯 perfect（完璧）", "😞 bad（悪い）", "🤔 okay（普通）"],
      answer_index: 0, rationale: "「Perfect pass!」のperfectは「完璧な」！"
    }],
    script: "A perfect pass to the striker!"
  },
  {
    clip_id: "stage2_005",
    meta: { difficulty: 2, duration_sec: 6, lexicon_tags: ["difficult", "英検4級"], wpm: 90, stage: 2 },
    questions: [{
      qid: "q1", prompt: "セーブの難易度は？",
      choices: ["😰 difficult（難しい）", "😊 easy（簡単）", "😐 normal（普通）"],
      answer_index: 0, rationale: "「Difficult save!」のdifficultは「難しい」！"
    }],
    script: "That was a difficult save!"
  }
];

// ユーティリティ関数
const vibrate = (pattern: number | number[]) => {
  if ('vibrate' in navigator) {
    navigator.vibrate(pattern);
  }
};

const speakText = (text: string) => {
  if ('speechSynthesis' in window) {
    const utterance = new SpeechSynthesisUtterance(text);
    utterance.lang = 'en-US';
    utterance.rate = 0.8;
    utterance.pitch = 1.0;
    utterance.volume = 0.8;
    speechSynthesis.speak(utterance);
  }
};

const playSuccessSound = () => {
  try {
    const audioContext = new (window.AudioContext || (window as any).webkitAudioContext)();
    const oscillator = audioContext.createOscillator();
    const gainNode = audioContext.createGain();
    
    oscillator.connect(gainNode);
    gainNode.connect(audioContext.destination);
    
    oscillator.frequency.setValueAtTime(523.25, audioContext.currentTime);
    oscillator.frequency.setValueAtTime(659.25, audioContext.currentTime + 0.1);
    oscillator.frequency.setValueAtTime(783.99, audioContext.currentTime + 0.2);
    
    gainNode.gain.setValueAtTime(0.3, audioContext.currentTime);
    gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.3);
    
    oscillator.start(audioContext.currentTime);
    oscillator.stop(audioContext.currentTime + 0.3);
    
    vibrate([100, 50, 100]);
  } catch (e) {
    console.log('Audio not supported');
  }
};

const playFailSound = () => {
  try {
    const audioContext = new (window.AudioContext || (window as any).webkitAudioContext)();
    const oscillator = audioContext.createOscillator();
    const gainNode = audioContext.createGain();
    
    oscillator.connect(gainNode);
    gainNode.connect(audioContext.destination);
    
    oscillator.frequency.setValueAtTime(220, audioContext.currentTime);
    oscillator.frequency.setValueAtTime(196, audioContext.currentTime + 0.15);
    
    gainNode.gain.setValueAtTime(0.3, audioContext.currentTime);
    gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.3);
    
    oscillator.start(audioContext.currentTime);
    oscillator.stop(audioContext.currentTime + 0.3);
    
    vibrate(200);
  } catch (e) {
    console.log('Audio not supported');
  }
};

const PreglishApp: React.FC = () => {
  // ゲーム状態
  const [gameState, setGameState] = useState<'home' | 'stage_select' | 'playing' | 'question' | 'stage_complete' | 'results'>('home');
  const [currentStage, setCurrentStage] = useState(1);
  const [currentQuestionInStage, setCurrentQuestionInStage] = useState(1);
  const [selectedAnswer, setSelectedAnswer] = useState<number | null>(null);
  const [showFeedback, setShowFeedback] = useState(false);
  const [hasRelistened, setHasRelistened] = useState(false);
  
  // アニメーション状態
  const [showScoreAnimation, setShowScoreAnimation] = useState(false);
  const [scoreAnimation, setScoreAnimation] = useState<'correct' | 'incorrect' | null>(null);
  const [confetti, setConfetti] = useState(false);
  
  // オーディオ状態
  const [isPlaying, setIsPlaying] = useState(false);
  
  // スコア・統計
  const [stats, setStats] = useState<PlayerStats>({
    totalScore: 0,
    streak: 0,
    averageResponseTime: 0,
    currentStage: 1,
    questionsCompleted: 0
  });
  const [questionStartTime, setQuestionStartTime] = useState<number>(0);
  const [lastScore, setLastScore] = useState(0);
  const [stageScore, setStageScore] = useState(0);

  // 現在のクリップを取得
  const getCurrentClip = () => {
    const stageClips = SAMPLE_CLIPS.filter(clip => clip.meta.stage === currentStage);
    return stageClips[(currentQuestionInStage - 1) % stageClips.length] || stageClips[0];
  };

  const currentClip = getCurrentClip();
  const currentQuestion = currentClip?.questions[0];
  const currentStageInfo = STAGES[currentStage - 1];

  // 音声再生
  const playCommentary = () => {
    if (!currentClip) return;
    vibrate(50);
    setIsPlaying(true);
    speakText(currentClip.script);
    
    setTimeout(() => {
      setIsPlaying(false);
      if (gameState === 'playing') {
        vibrate(30);
        setGameState('question');
        setQuestionStartTime(Date.now());
      }
    }, currentClip.meta.duration_sec * 1000);
  };

  const relistenAudio = () => {
    if (!hasRelistened) {
      setHasRelistened(true);
      playCommentary();
    }
  };

  // 回答処理
  const handleAnswer = (answerIndex: number) => {
    if (selectedAnswer !== null || !currentQuestion) return;
    
    setSelectedAnswer(answerIndex);
    const responseTime = (Date.now() - questionStartTime) / 1000;
    const isCorrect = answerIndex === currentQuestion.answer_index;
    
    if (isCorrect) {
      playSuccessSound();
      setScoreAnimation('correct');
    } else {
      playFailSound();
      setScoreAnimation('incorrect');
    }
    
    // スコア計算
    let score = isCorrect ? 100 : 0;
    const speedBonus = Math.max(0, 40 - responseTime * 8);
    const difficultyMultiplier = 1 + (currentStage - 1) * 0.25;
    const relistenPenalty = hasRelistened ? 0.8 : 1.0;
    
    score = (score + speedBonus) * difficultyMultiplier * relistenPenalty;
    setLastScore(Math.round(score));
    
    setStats(prev => ({
      ...prev,
      totalScore: prev.totalScore + score,
      streak: isCorrect ? prev.streak + 1 : 0,
      averageResponseTime: (prev.averageResponseTime + responseTime) / 2,
      questionsCompleted: prev.questionsCompleted + 1
    }));
    
    setStageScore(prev => prev + Math.round(score));
    setShowScoreAnimation(true);
    
    setTimeout(() => {
      setShowFeedback(true);
      setScoreAnimation(null);
      setShowScoreAnimation(false);
    }, 1500);
  };

  // 次の問題へ
  const nextQuestion = () => {
    if (currentQuestionInStage < 5) {
      setCurrentQuestionInStage(prev => prev + 1);
      setSelectedAnswer(null);
      setShowFeedback(false);
      setHasRelistened(false);
      setGameState('playing');
    } else {
      completeStage();
    }
  };

  // ステージ完了
  const completeStage = () => {
    vibrate([200, 100, 200]);
    setGameState('stage_complete');
    setConfetti(true);
    setTimeout(() => {
      setConfetti(false);
      if (currentStage < STAGES.length) {
        setTimeout(() => {
          nextStage();
        }, 2000);
      }
    }, 3000);
  };

  // 次のステージへ
  const nextStage = () => {
    if (currentStage < STAGES.length) {
      setCurrentStage(prev => prev + 1);
      setCurrentQuestionInStage(1);
      setStageScore(0);
      setSelectedAnswer(null);
      setShowFeedback(false);
      setHasRelistened(false);
      setGameState('playing');
    } else {
      setGameState('results');
    }
  };

  // ステージ選択
  const selectStage = (stageId: number) => {
    setCurrentStage(stageId);
    setCurrentQuestionInStage(1);
    setStageScore(0);
    setStats(prev => ({ ...prev, currentStage: stageId, questionsCompleted: 0 }));
    setGameState('playing');
  };

  // ゲーム開始
  const startGame = () => {
    vibrate([50, 100, 50, 100, 50]);
    setGameState('stage_select');
  };

  // ゲームリセット
  const resetGame = () => {
    setGameState('home');
    setCurrentStage(1);
    setCurrentQuestionInStage(1);
    setSelectedAnswer(null);
    setShowFeedback(false);
    setHasRelistened(false);
    setIsPlaying(false);
    setStageScore(0);
    setStats({
      totalScore: 0,
      streak: 0,
      averageResponseTime: 0,
      currentStage: 1,
      questionsCompleted: 0
    });
  };

  return (
    <div className="min-h-screen bg-black text-white relative overflow-hidden">
      {/* カスタムアニメーション */}
      <style dangerouslySetInnerHTML={{
        __html: `
          @keyframes float {
            0%, 100% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(-20px) rotate(180deg); }
          }
          @keyframes confetti {
            0% { transform: translateY(-100vh) rotate(0deg); opacity: 1; }
            100% { transform: translateY(100vh) rotate(720deg); opacity: 0; }
          }
          @keyframes score-pop {
            0% { transform: scale(0) rotate(-180deg); opacity: 0; }
            50% { transform: scale(1.2) rotate(0deg); opacity: 1; }
            100% { transform: scale(1) rotate(0deg); opacity: 1; }
          }
          @keyframes celebration {
            0%, 100% { transform: scale(1) rotate(0deg); }
            25% { transform: scale(1.1) rotate(5deg); }
            75% { transform: scale(1.1) rotate(-5deg); }
          }
          @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-10px); }
            75% { transform: translateX(10px); }
          }
          .animate-float { animation: float 6s ease-in-out infinite; }
          .animate-confetti { animation: confetti 4s linear infinite; }
          .animate-score-pop { animation: score-pop 0.6s cubic-bezier(0.68, -0.55, 0.265, 1.55); }
          .animate-celebration { animation: celebration 0.6s ease-in-out; }
          .animate-shake { animation: shake 0.5s ease-in-out; }
        `
      }} />

      {/* 動的背景 */}
      <div className="absolute inset-0 bg-gradient-to-br from-purple-900/30 via-blue-900/20 to-green-900/30 animate-pulse"></div>
      
      {/* メッシュグラデーション */}
      <div className="absolute inset-0 opacity-20">
        <div className="absolute top-0 left-0 w-96 h-96 bg-gradient-conic from-purple-500 via-blue-500 to-green-500 rounded-full blur-3xl animate-pulse"></div>
        <div className="absolute bottom-0 right-0 w-96 h-96 bg-gradient-conic from-green-500 via-yellow-500 to-red-500 rounded-full blur-3xl animate-pulse"></div>
        <div className="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 w-64 h-64 bg-gradient-conic from-pink-500 via-purple-500 to-blue-500 rounded-full blur-2xl animate-spin"></div>
      </div>

      {/* 浮遊パーティクル */}
      <div className="absolute inset-0 overflow-hidden">
        {Array.from({length: 15}).map((_, i) => (
          <div
            key={i}
            className="absolute w-2 h-2 bg-white/20 rounded-full animate-float"
            style={{
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
              animationDelay: `${Math.random() * 5}s`,
              animationDuration: `${3 + Math.random() * 4}s`
            }}
          ></div>
        ))}
      </div>

      {/* 紙吹雪エフェクト */}
      {confetti && (
        <div className="fixed inset-0 pointer-events-none z-50">
          {Array.from({length: 50}).map((_, i) => (
            <div
              key={i}
              className={`absolute w-3 h-3 rounded-full animate-confetti ${
                i % 4 === 0 ? 'bg-yellow-400' :
                i % 4 === 1 ? 'bg-pink-400' :
                i % 4 === 2 ? 'bg-blue-400' : 'bg-green-400'
              }`}
              style={{
                left: `${Math.random() * 100}%`,
                top: `-10px`,
                animationDelay: `${Math.random() * 2}s`,
                animationDuration: `${2 + Math.random() * 2}s`
              }}
            ></div>
          ))}
        </div>
      )}

      <div className="max-w-6xl mx-auto p-6 relative z-10">
        {/* ヘッダー */}
        <header className="text-center mb-12">
          <div className="inline-block p-8 rounded-3xl bg-gradient-to-r from-purple-500/20 to-blue-500/20 backdrop-blur-xl border border-white/10 shadow-2xl transform hover:scale-105 transition-all duration-500">
            <h1 className="text-6xl font-black bg-gradient-to-r from-purple-400 via-pink-400 to-blue-400 bg-clip-text text-transparent mb-4 animate-pulse">
              PREGLISH
            </h1>
            <div className="flex items-center justify-center gap-4 text-xl text-gray-300">
              <div className="w-2 h-2 bg-purple-400 rounded-full animate-ping"></div>
              <span>Soccer × English × AI</span>
              <div className="w-2 h-2 bg-blue-400 rounded-full animate-ping"></div>
            </div>
          </div>
        </header>

        {/* ホーム画面 */}
        {gameState === 'home' && (
          <div className="space-y-8">
            <div className="text-center p-12 rounded-3xl bg-gradient-to-br from-gray-900/60 to-gray-800/60 backdrop-blur-xl border border-white/10 shadow-2xl hover:shadow-purple-500/20 transition-all duration-500 transform hover:scale-102">
              <div className="w-20 h-20 mx-auto mb-6 rounded-full bg-gradient-to-r from-purple-500 to-blue-500 flex items-center justify-center">
                <Play size={40} className="text-white ml-1 animate-bounce" />
              </div>
              <h2 className="text-4xl font-bold mb-6 bg-gradient-to-r from-white to-gray-300 bg-clip-text text-transparent">
                Ready to Play?
              </h2>
              <p className="text-xl text-gray-300 mb-8 leading-relaxed max-w-2xl mx-auto">
                サッカー実況で英検単語をマスター<br />
                AI音声 × リアルタイム判定 × 段階学習
              </p>
              <button
                onClick={startGame}
                className="group relative px-12 py-4 rounded-2xl bg-gradient-to-r from-purple-600 to-blue-600 hover:from-purple-500 hover:to-blue-500 transition-all duration-300 transform hover:scale-110 active:scale-95 shadow-xl hover:shadow-2xl"
              >
                <div className="absolute inset-0 rounded-2xl bg-gradient-to-r from-purple-400 to-blue-400 opacity-0 group-hover:opacity-30 transition-opacity duration-300 animate-pulse"></div>
                <span className="relative text-xl font-bold text-white flex items-center gap-3">
                  <Play size={24} className="animate-bounce" />
                  START GAME
                </span>
              </button>
            </div>
            
            <div className="grid md:grid-cols-3 gap-6">
              <div className="p-8 rounded-2xl bg-gradient-to-br from-purple-500/20 to-purple-600/20 backdrop-blur-xl border border-white/10 hover:border-white/20 transition-all duration-300 hover:scale-105">
                <div className="text-4xl mb-4">📚</div>
                <h3 className="font-bold text-xl mb-3 text-white">英検対策</h3>
                <p className="text-gray-300">5級→4級→3級の頻出語彙</p>
              </div>
              <div className="p-8 rounded-2xl bg-gradient-to-br from-blue-500/20 to-blue-600/20 backdrop-blur-xl border border-white/10 hover:border-white/20 transition-all duration-300 hover:scale-105">
                <div className="text-4xl mb-4">⚽</div>
                <h3 className="font-bold text-xl mb-3 text-white">実況音声</h3>
                <p className="text-gray-300">リアルな英語でスポーツ体験</p>
              </div>
              <div className="p-8 rounded-2xl bg-gradient-to-br from-green-500/20 to-green-600/20 backdrop-blur-xl border border-white/10 hover:border-white/20 transition-all duration-300 hover:scale-105">
                <div className="text-4xl mb-4">🎯</div>
                <h3 className="font-bold text-xl mb-3 text-white">AI判定</h3>
                <p className="text-gray-300">瞬時フィードバック＆成長記録</p>
              </div>
            </div>
          </div>
        )}

        {/* ステージ選択画面 */}
        {gameState === 'stage_select' && (
          <div className="space-y-8">
            <div className="text-center p-8 rounded-3xl bg-gradient-to-br from-purple-900/60 to-blue-900/60 backdrop-blur-xl border border-white/10 shadow-2xl">
              <h2 className="text-4xl font-black mb-4 bg-gradient-to-r from-purple-400 to-blue-400 bg-clip-text text-transparent">
                SELECT STAGE
              </h2>
              <p className="text-xl text-gray-300">Choose your difficulty level</p>
            </div>

            <div className="grid md:grid-cols-2 gap-6">
              {STAGES.map((stage) => {
                const isUnlocked = stage.id === 1 || stats.totalScore >= stage.minScore;
                return (
                  <div 
                    key={stage.id} 
                    className={`relative p-8 rounded-3xl backdrop-blur-xl border transition-all duration-300 ${
                      isUnlocked 
                        ? 'bg-gradient-to-br from-blue-500/20 to-purple-500/20 border-white/20 hover:border-white/40 hover:scale-105 cursor-pointer'
                        : 'bg-gray-900/40 border-gray-700/50 opacity-50'
                    }`} 
                    onClick={() => isUnlocked && selectStage(stage.id)}
                  >
                    {!isUnlocked && (
                      <div className="absolute inset-0 flex items-center justify-center bg-black/60 rounded-3xl">
                        <div className="text-center">
                          <div className="text-4xl mb-2">🔒</div>
                          <div className="text-white font-bold">スコア {stage.minScore} で解放</div>
                        </div>
                      </div>
                    )}
                    
                    <div className="flex items-center gap-4 mb-4">
                      <div className="w-12 h-12 rounded-full bg-gradient-to-r from-blue-500 to-purple-500 flex items-center justify-center text-white font-bold text-xl">
                        {stage.id}
                      </div>
                      <div>
                        <h3 className="text-xl font-bold text-white">{stage.name}</h3>
                        <p className="text-gray-300">{stage.theme}</p>
                      </div>
                    </div>
                    
                    <p className="text-gray-300 mb-4">{stage.description}</p>
                    
                    <div className="flex justify-between items-center">
                      <span className="text-sm text-gray-400">5 Questions</span>
                      {isUnlocked && (
                        <div className="px-4 py-2 rounded-full bg-white/10 text-white font-semibold">
                          START
                        </div>
                      )}
                    </div>
                  </div>
                );
              })}
            </div>
          </div>
        )}

        {/* ゲーム画面 */}
        {(gameState === 'playing' || gameState === 'question') && (
          <div className="space-y-8">
            {/* HUD */}
            <div className="flex justify-between items-center p-6 rounded-2xl bg-gray-900/60 backdrop-blur-xl border border-white/10">
              <div className="flex items-center gap-6">
                <div className="flex items-center gap-2">
                  <div className="w-8 h-8 rounded-full bg-gradient-to-r from-blue-500 to-purple-500 flex items-center justify-center text-white font-bold text-sm">
                    {currentStage}
                  </div>
                  <div>
                    <div className="text-lg font-bold text-white">{currentStageInfo.name}</div>
                    <div className="text-sm text-gray-400">{currentStageInfo.theme}</div>
                  </div>
                </div>
                <div className="flex items-center gap-2">
                  <Target className="text-green-400" size={20} />
                  <span className="text-white font-semibold">{currentQuestionInStage}/5</span>
                </div>
              </div>
              <div className="flex items-center gap-4">
                <div className="text-center">
                  <div className="text-sm text-gray-400">Stage Score</div>
                  <div className="text-xl font-bold text-yellow-400">{stageScore}</div>
                </div>
                <div className="text-center">
                  <div className="text-sm text-gray-400">Total</div>
                  <div className="text-xl font-bold text-green-400">{Math.round(stats.totalScore)}</div>
                </div>
              </div>
            </div>

            {/* プログレスバー */}
            <div className="relative">
              <div className="w-full h-3 bg-gray-800 rounded-full overflow-hidden">
                <div 
                  className="h-full bg-gradient-to-r from-blue-500 to-purple-500 transition-all duration-500"
                  style={{ width: `${(currentQuestionInStage / 5) * 100}%` }}
                ></div>
              </div>
              <div className="absolute -top-2 right-0 transform translate-x-2">
                <div className="text-xs text-gray-400 font-semibold">
                  {currentQuestionInStage}/5
                </div>
              </div>
            </div>

            {/* スコアアニメーション */}
            {showScoreAnimation && (
              <div className="fixed inset-0 flex items-center justify-center z-50 pointer-events-none">
                <div className={`text-8xl font-black animate-score-pop ${
                  scoreAnimation === 'correct' ? 'text-green-400' : 'text-red-400'
                }`}>
                  {scoreAnimation === 'correct' ? (
                    <div className="flex items-center gap-4 animate-celebration">
                      <span className="animate-bounce">+{lastScore}</span>
                      <span className="text-6xl animate-spin">🎉</span>
                    </div>
                  ) : (
                    <div className="flex items-center gap-4 animate-shake">
                      <span>MISS</span>
                      <span className="text-6xl animate-pulse">💧</span>
                    </div>
                  )}
                </div>
              </div>
            )}

            {/* 音声プレイヤー */}
            {gameState === 'playing' && currentClip && (
              <div className="text-center p-12 rounded-3xl bg-gradient-to-br from-blue-900/40 to-purple-900/40 backdrop-blur-xl border border-white/10 transform hover:scale-102 transition-all duration-500">
                <div className="w-24 h-24 mx-auto mb-8 rounded-full bg-gradient-to-r from-blue-500 to-purple-500 flex items-center justify-center shadow-2xl">
                  <Volume2 size={40} className="text-white animate-pulse" />
                </div>
                <h3 className="text-3xl font-bold mb-4 text-white">Question {currentQuestionInStage}</h3>
                <p className="text-lg text-gray-300 mb-8">{currentStageInfo.description}</p>
                <button
                  onClick={playCommentary}
                  disabled={isPlaying}
                  className="group relative px-12 py-4 rounded-2xl bg-gradient-to-r from-blue-600 to-purple-600 hover:from-blue-500 hover:to-purple-500 transition-all duration-300 transform hover:scale-110 active:scale-95 shadow-xl disabled:opacity-50"
                >
                  <div className="absolute inset-0 rounded-2xl bg-gradient-to-r from-blue-400 to-purple-400 opacity-0 group-hover:opacity-30 transition-opacity duration-300 animate-pulse"></div>
                  <span className="relative text-xl font-bold text-white flex items-center gap-3">
                    {isPlaying ? (
                      <>
                        <div className="w-6 h-6 rounded-full border-2 border-white/30 border-t-white animate-spin"></div>
                        <span className="animate-pulse">PLAYING...</span>
                      </>
                    ) : (
                      <>
                        <Play size={24} className="animate-bounce" />
                        <span>PLAY AUDIO</span>
                      </>
                    )}
                  </span>
                </button>
                <div className="mt-6 text-gray-400">
                  {currentClip.meta.duration_sec}s | {currentClip.meta.lexicon_tags.join(' • ')}
                </div>
              </div>
            )}

            {/* 問題 */}
            {gameState === 'question' && currentQuestion && (
              <div className="p-8 rounded-3xl bg-gray-900/60 backdrop-blur-xl border border-white/10">
                <div className="flex justify-between items-center mb-8">
                  <h3 className="text-2xl font-bold text-white flex items-center gap-3">
                    <Target className="text-green-400" />
                    Question {currentQuestionInStage} of 5
                  </h3>
                  {!hasRelistened && (
                    <button
                      onClick={relistenAudio}
                      className="px-4 py-2 rounded-xl bg-orange-500/20 hover:bg-orange-500/30 transition-colors flex items-center gap-2 text-orange-400 border border-orange-500/30"
                    >
                      <RotateCcw size={16} />
                      Replay
                    </button>
                  )}
                </div>
                
                <div className="mb-8 p-6 rounded-2xl bg-gradient-to-r from-yellow-500/20 to-orange-500/20 border border-yellow-500/30">
                  <p className="text-2xl font-semibold text-white">{currentQuestion.prompt}</p>
                </div>
                
                <div className="grid gap-4">
                  {currentQuestion.choices.map((choice, index) => (
                    <button
                      key={index}
                      onClick={() => handleAnswer(index)}
                      disabled={selectedAnswer !== null}
                      className={`group relative p-6 rounded-2xl text-left transition-all duration-300 transform hover:scale-102 active:scale-95 text-lg font-semibold ${
                        selectedAnswer === null
                          ? 'bg-gray-800/60 hover:bg-gray-700/60 border border-gray-600/50 hover:border-blue-500/50 text-white'
                          : selectedAnswer === index
                          ? index === currentQuestion.answer_index
                            ? 'bg-green-500/20 border border-green-500 text-green-400'
                            : 'bg-red-500/20 border border-red-500 text-red-400'
                          : index === currentQuestion.answer_index
                          ? 'bg-green-500/20 border border-green-500 text-green-400'
                          : 'bg-gray-800/40 border border-gray-600/30 text-gray-500'
                      }`}
                    >
                      <div className="flex items-center gap-4">
                        <div className={`w-8 h-8 rounded-full flex items-center justify-center font-bold ${
                          selectedAnswer === null 
                            ? 'bg-blue-500/20 text-blue-400' 
                            : selectedAnswer === index
                            ? index === currentQuestion.answer_index
                              ? 'bg-green-500 text-white'
                              : 'bg-red-500 text-white'
                            : index === currentQuestion.answer_index
                            ? 'bg-green-500 text-white'
                            : 'bg-gray-600 text-gray-400'
                        }`}>
                          {String.fromCharCode(65 + index)}
                        </div>
                        <span>{choice}</span>
                      </div>
                    </button>
                  ))}
                </div>

                {/* フィードバック */}
                {showFeedback && (
                  <div className="mt-8 p-6 rounded-2xl bg-gradient-to-br from-blue-900/40 to-green-900/40 backdrop-blur-xl border border-white/10">
                    <h4 className="font-bold text-xl mb-4 text-white flex items-center gap-2">
                      💡 解説
                    </h4>
                    <p className="mb-4 text-lg text-gray-300">{currentQuestion.rationale}</p>
                    <div className="mb-4 p-4 rounded-xl bg-black/40 border border-gray-700/50">
                      <div className="text-sm text-gray-400 mb-2">Script:</div>
                      <div className="text-blue-400 font-mono">"{currentClip.script}"</div>
                    </div>
                    <div className="mb-6">
                      <div className="text-sm text-gray-400 mb-2">Keywords:</div>
                      <div className="flex flex-wrap gap-2">
                        {currentClip.meta.lexicon_tags.map(tag => (
                          <span key={tag} className="px-3 py-1 rounded-full bg-yellow-500/20 text-yellow-400 text-sm font-medium border border-yellow-500/30">
                            {tag}
                          </span>
                        ))}
                      </div>
                    </div>
                    <button
                      onClick={nextQuestion}
                      className="group relative px-8 py-3 rounded-xl bg-gradient-to-r from-green-600 to-blue-600 hover:from-green-500 hover:to-blue-500 transition-all duration-300 transform hover:scale-105 active:scale-95 shadow-xl"
                    >
                      <div className="absolute inset-0 rounded-xl bg-gradient-to-r from-green-400 to-blue-400 opacity-0 group-hover:opacity-20 transition-opacity duration-300"></div>
                      <span className="relative font-bold text-white flex items-center gap-2">
                        {currentQuestionInStage < 5 ? 'NEXT QUESTION' : 'COMPLETE STAGE'}
                        <Zap size={16} />
                      </span>
                    </button>
                  </div>
                )}
              </div>
            )}
          </div>
        )}

        {/* ステージ完了画面 */}
        {gameState === 'stage_complete' && (
          <div className="space-y-8">
            <div className="text-center p-12 rounded-3xl bg-gradient-to-br from-purple-900/60 to-blue-900/60 backdrop-blur-xl border border-white/10 shadow-2xl">
              <div className="w-32 h-32 mx-auto mb-8 rounded-full bg-gradient-to-r from-blue-500 to-purple-500 flex items-center justify-center shadow-2xl">
                <Trophy size={64} className="text-white" />
              </div>
              <h2 className="text-5xl font-black mb-4 bg-gradient-to-r from-yellow-400 to-orange-500 bg-clip-text text-transparent">
                STAGE {currentStage} COMPLETE!
              </h2>
              <h3 className="text-2xl font-bold mb-8 text-white">{currentStageInfo.name}</h3>
              
              <div className="grid md:grid-cols-3 gap-6 mb-12">
                <div className="p-6 rounded-2xl bg-gradient-to-br from-blue-500/20 to-blue-600/20 backdrop-blur-xl border border-white/10">
                  <div className="text-3xl mb-2">🎯</div>
                  <div className="text-sm text-gray-300 mb-1">Stage Score</div>
                  <div className="text-3xl font-bold text-white">{stageScore}</div>
                </div>
                <div className="p-6 rounded-2xl bg-gradient-to-br from-green-500/20 to-green-600/20 backdrop-blur-xl border border-white/10">
                  <div className="text-3xl mb-2">📊</div>
                  <div className="text-sm text-gray-300 mb-1">Total Score</div>
                  <div className="text-3xl font-bold text-white">{Math.round(stats.totalScore)}</div>
                </div>
                <div className="p-6 rounded-2xl bg-gradient-to-br from-purple-500/20 to-purple-600/20 backdrop-blur-xl border border-white/10">
                  <div className="text-3xl mb-2">🔥</div>
                  <div className="text-sm text-gray-300 mb-1">Streak</div>
                  <div className="text-3xl font-bold text-white">{stats.streak}</div>
                </div>
              </div>

              {currentStage < STAGES.length ? (
                <div className="space-y-6">
                  <div className="text-xl text-gray-300 mb-4 animate-pulse">
                    🎉 素晴らしい！次のステージに進みます...
                  </div>
                  <div className="flex items-center justify-center gap-4">
                    <div className="w-4 h-4 bg-blue-400 rounded-full animate-bounce"></div>
                    <div className="w-4 h-4 bg-purple-400 rounded-full animate-bounce" style={{animationDelay: '0.1s'}}></div>
                    <div className="w-4 h-4 bg-pink-400 rounded-full animate-bounce" style={{animationDelay: '0.2s'}}></div>
                  </div>
                  <div className="text-2xl font-bold text-white">
                    <span className="animate-pulse">Next: </span>
                    <span className="bg-gradient-to-r from-yellow-400 to-orange-500 bg-clip-text text-transparent">
                      {STAGES[currentStage]?.name}
                    </span>
                  </div>
                  <div className="text-lg text-gray-400">
                    自動で開始します... 🚀
                  </div>
                </div>
              ) : (
                <div className="flex gap-4 justify-center">
                  <button
                    onClick={() => setGameState('results')}
                    className="group relative px-8 py-4 rounded-2xl bg-gradient-to-r from-yellow-600 to-red-600 hover:from-yellow-500 hover:to-red-500 transition-all duration-300 transform hover:scale-105 active:scale-95 shadow-xl"
                  >
                    <div className="absolute inset-0 rounded-2xl bg-gradient-to-r from-yellow-400 to-red-400 opacity-0 group-hover:opacity-20 transition-opacity duration-300"></div>
                    <span className="relative font-bold text-white text-xl flex items-center gap-2">
                      <Trophy size={20} />
                      VIEW RESULTS
                    </span>
                  </button>
                  <button
                    onClick={() => setGameState('stage_select')}
                    className="group relative px-8 py-4 rounded-2xl bg-gray-700/60 hover:bg-gray-600/60 transition-all duration-300 transform hover:scale-105 active:scale-95 border border-gray-600/50"
                  >
                    <span className="relative font-bold text-white text-xl">
                      STAGE SELECT
                    </span>
                  </button>
                </div>
              )}
            </div>
          </div>
        )}

        {/* 最終結果画面 */}
        {gameState === 'results' && (
          <div className="space-y-8">
            <div className="text-center p-12 rounded-3xl bg-gradient-to-br from-purple-900/60 to-blue-900/60 backdrop-blur-xl border border-white/10 shadow-2xl">
              <div className="w-32 h-32 mx-auto mb-8 rounded-full bg-gradient-to-r from-yellow-400 to-orange-500 flex items-center justify-center shadow-2xl">
                <Award size={64} className="text-white" />
              </div>
              <h2 className="text-5xl font-black mb-8 bg-gradient-to-r from-yellow-400 to-orange-500 bg-clip-text text-transparent">
                GAME COMPLETE!
              </h2>
              
              <div className="grid md:grid-cols-4 gap-6 mb-12">
                <div className="p-6 rounded-2xl bg-gradient-to-br from-yellow-500/20 to-yellow-600/20 backdrop-blur-xl border border-white/10">
                  <div className="text-3xl mb-2">🏆</div>
                  <div className="text-sm text-gray-300 mb-1">Total Score</div>
                  <div className="text-2xl font-bold text-white">{Math.round(stats.totalScore)}</div>
                </div>
                <div className="p-6 rounded-2xl bg-gradient-to-br from-green-500/20 to-green-600/20 backdrop-blur-xl border border-white/10">
                  <div className="text-3xl mb-2">🎯</div>
                  <div className="text-sm text-gray-300 mb-1">Questions</div>
                  <div className="text-2xl font-bold text-white">{stats.questionsCompleted}</div>
                </div>
                <div className="p-6 rounded-2xl bg-gradient-to-br from-red-500/20 to-red-600/20 backdrop-blur-xl border border-white/10">
                  <div className="text-3xl mb-2">🔥</div>
                  <div className="text-sm text-gray-300 mb-1">Best Streak</div>
                  <div className="text-2xl font-bold text-white">{stats.streak}</div>
                </div>
                <div className="p-6 rounded-2xl bg-gradient-to-br from-blue-500/20 to-blue-600/20 backdrop-blur-xl border border-white/10">
                  <div className="text-3xl mb-2">⚡</div>
                  <div className="text-sm text-gray-300 mb-1">Avg Speed</div>
                  <div className="text-2xl font-bold text-white">{stats.averageResponseTime.toFixed(1)}s</div>
                </div>
              </div>

              <div className="flex gap-4 justify-center">
                <button
                  onClick={() => setGameState('stage_select')}
                  className="group relative px-8 py-4 rounded-2xl bg-gradient-to-r from-green-600 to-blue-600 hover:from-green-500 hover:to-blue-500 transition-all duration-300 transform hover:scale-105 active:scale-95 shadow-xl"
                >
                  <div className="absolute inset-0 rounded-2xl bg-gradient-to-r from-green-400 to-blue-400 opacity-0 group-hover:opacity-20 transition-opacity duration-300"></div>
                  <span className="relative font-bold text-white text-xl flex items-center gap-2">
                    <RotateCcw size={20} />
                    PLAY AGAIN
                  </span>
                </button>
                <button
                  onClick={resetGame}
                  className="group relative px-8 py-4 rounded-2xl bg-gray-700/60 hover:bg-gray-600/60 transition-all duration-300 transform hover:scale-105 active:scale-95 border border-gray-600/50"
                >
                  <span className="relative font-bold text-white text-xl">
                    HOME
                  </span>
                </button>
              </div>
            </div>
          </div>
        )}
      </div>
    </div>
  );
};

export default PreglishApp;
