/**
 * @license
 * SPDX-License-Identifier: Apache-2.0
 */

import React, { useState, useEffect, useCallback, useMemo } from 'react';
import { motion, AnimatePresence } from 'motion/react';
import { Heart, Diamond, Club, Spade, RotateCcw, Trophy, User, Cpu, Info } from 'lucide-react';
import { Suit, Rank, Card, GameStatus, GameState } from './types';
import { createDeck, shuffle } from './constants';

const SuitIcon = ({ suit, className = "" }: { suit: Suit; className?: string }) => {
  switch (suit) {
    case Suit.HEARTS: return <Heart className={`fill-red-500 text-red-500 ${className}`} />;
    case Suit.DIAMONDS: return <Diamond className={`fill-red-500 text-red-500 ${className}`} />;
    case Suit.CLUBS: return <Club className={`fill-slate-800 text-slate-800 ${className}`} />;
    case Suit.SPADES: return <Spade className={`fill-slate-800 text-slate-800 ${className}`} />;
  }
};

const PlayingCard = ({ 
  card, 
  isFaceDown = false, 
  onClick, 
  isPlayable = false,
  className = "" 
}: { 
  card?: Card; 
  isFaceDown?: boolean; 
  onClick?: () => void; 
  isPlayable?: boolean;
  className?: string;
}) => {
  if (isFaceDown) {
    return (
      <div 
        className={`w-16 h-24 sm:w-24 sm:h-36 bg-white rounded-lg border-2 border-slate-200 shadow-md flex items-center justify-center relative overflow-hidden ${className}`}
      >
        <div className="absolute inset-1 bg-blue-800 rounded-md border border-white/20 flex items-center justify-center">
          <div className="w-full h-full opacity-20 bg-[radial-gradient(circle_at_center,_var(--tw-gradient-stops))] from-white via-transparent to-transparent" />
          <div className="text-white/40 font-bold text-xl select-none">8</div>
        </div>
      </div>
    );
  }

  if (!card) return null;

  const isRed = card.suit === Suit.HEARTS || card.suit === Suit.DIAMONDS;

  return (
    <motion.div
      whileHover={isPlayable ? { y: -10, scale: 1.05 } : {}}
      onClick={isPlayable ? onClick : undefined}
      className={`
        w-16 h-24 sm:w-24 sm:h-36 bg-white rounded-lg border-2 shadow-md flex flex-col p-1 sm:p-2 relative select-none
        ${isPlayable ? 'cursor-pointer border-yellow-400 ring-2 ring-yellow-400/50' : 'border-slate-200'}
        ${className}
      `}
    >
      <div className={`flex flex-col items-center self-start leading-none ${isRed ? 'text-red-500' : 'text-slate-800'}`}>
        <span className="text-sm sm:text-lg font-bold">{card.rank}</span>
        <SuitIcon suit={card.suit} className="w-3 h-3 sm:w-4 sm:h-4" />
      </div>
      
      <div className="flex-1 flex items-center justify-center">
        <SuitIcon suit={card.suit} className="w-6 h-6 sm:w-10 sm:h-10 opacity-80" />
      </div>

      <div className={`flex flex-col items-center self-end leading-none rotate-180 ${isRed ? 'text-red-500' : 'text-slate-800'}`}>
        <span className="text-sm sm:text-lg font-bold">{card.rank}</span>
        <SuitIcon suit={card.suit} className="w-3 h-3 sm:w-4 sm:h-4" />
      </div>
    </motion.div>
  );
};

export default function App() {
  const [gameState, setGameState] = useState<GameState | null>(null);
  const [showSuitSelector, setShowSuitSelector] = useState(false);
  const [lastPlayedCard, setLastPlayedCard] = useState<Card | null>(null);

  const initGame = useCallback(() => {
    const fullDeck = shuffle(createDeck());
    const playerHand = fullDeck.splice(0, 8);
    const aiHand = fullDeck.splice(0, 8);
    
    // Ensure first discard is not an 8
    let discardIndex = 0;
    while (fullDeck[discardIndex].rank === Rank.EIGHT) {
      discardIndex++;
    }
    const discardPile = [fullDeck.splice(discardIndex, 1)[0]];

    setGameState({
      deck: fullDeck,
      discardPile,
      playerHand,
      aiHand,
      currentTurn: 'player',
      status: 'playing',
      declaredSuit: null,
    });
    setLastPlayedCard(null);
  }, []);

  useEffect(() => {
    initGame();
  }, [initGame]);

  const topCard = gameState?.discardPile[gameState.discardPile.length - 1];

  const isCardPlayable = useCallback((card: Card) => {
    if (!gameState || !topCard) return false;
    
    // 8 is wild
    if (card.rank === Rank.EIGHT) return true;

    // If suit was declared by an 8
    if (gameState.declaredSuit) {
      return card.suit === gameState.declaredSuit;
    }

    // Match suit or rank
    return card.suit === topCard.suit || card.rank === topCard.rank;
  }, [gameState, topCard]);

  const checkWinner = (state: GameState) => {
    if (state.playerHand.length === 0) return 'player_won';
    if (state.aiHand.length === 0) return 'ai_won';
    return state.status;
  };

  const playCard = (card: Card, isPlayer: boolean) => {
    if (!gameState) return;

    const handKey = isPlayer ? 'playerHand' : 'aiHand';
    const nextTurn = isPlayer ? 'ai' : 'player';
    
    const newHand = gameState[handKey].filter(c => c.id !== card.id);
    const newDiscardPile = [...gameState.discardPile, card];
    
    let newStatus: GameStatus = 'playing';
    if (newHand.length === 0) {
      newStatus = isPlayer ? 'player_won' : 'ai_won';
    } else if (card.rank === Rank.EIGHT) {
      newStatus = 'waiting_for_suit';
    }

    setGameState(prev => {
      if (!prev) return null;
      return {
        ...prev,
        [handKey]: newHand,
        discardPile: newDiscardPile,
        currentTurn: card.rank === Rank.EIGHT && isPlayer ? 'player' : nextTurn,
        status: newStatus,
        declaredSuit: null, // Reset declared suit on any play
      };
    });

    if (card.rank === Rank.EIGHT && isPlayer) {
      setShowSuitSelector(true);
    }
  };

  const declareSuit = (suit: Suit) => {
    setGameState(prev => {
      if (!prev) return null;
      return {
        ...prev,
        declaredSuit: suit,
        status: 'playing',
        currentTurn: 'ai',
      };
    });
    setShowSuitSelector(false);
  };

  const drawCard = (isPlayer: boolean) => {
    if (!gameState || gameState.status !== 'playing') return;
    if (gameState.deck.length === 0) {
      // Skip turn if deck empty
      setGameState(prev => prev ? { ...prev, currentTurn: isPlayer ? 'ai' : 'player' } : null);
      return;
    }

    const newDeck = [...gameState.deck];
    const drawnCard = newDeck.pop()!;
    const handKey = isPlayer ? 'playerHand' : 'aiHand';

    setGameState(prev => {
      if (!prev) return null;
      return {
        ...prev,
        deck: newDeck,
        [handKey]: [...prev[handKey], drawnCard],
        currentTurn: isPlayer ? 'ai' : 'player',
      };
    });
  };

  // AI Logic
  useEffect(() => {
    if (gameState?.currentTurn === 'ai' && gameState.status === 'playing') {
      const timer = setTimeout(() => {
        const playableCards = gameState.aiHand.filter(isCardPlayable);
        
        if (playableCards.length > 0) {
          // AI Strategy: Prefer non-8s first, then 8s
          const nonEight = playableCards.find(c => c.rank !== Rank.EIGHT);
          const cardToPlay = nonEight || playableCards[0];
          
          playCard(cardToPlay, false);

          // If AI played an 8, it needs to declare a suit
          if (cardToPlay.rank === Rank.EIGHT) {
            // AI Strategy for suit: pick the suit it has most of
            const suitCounts: Record<string, number> = {};
            gameState.aiHand.forEach(c => {
              suitCounts[c.suit] = (suitCounts[c.suit] || 0) + 1;
            });
            let bestSuit = Suit.HEARTS;
            let maxCount = -1;
            Object.entries(suitCounts).forEach(([suit, count]) => {
              if (count > maxCount) {
                maxCount = count;
                bestSuit = suit as Suit;
              }
            });
            
            setTimeout(() => {
              setGameState(prev => prev ? { ...prev, declaredSuit: bestSuit, currentTurn: 'player' } : null);
            }, 600);
          }
        } else {
          drawCard(false);
        }
      }, 1500);
      return () => clearTimeout(timer);
    }
  }, [gameState?.currentTurn, gameState?.status, isCardPlayable]);

  if (!gameState) return null;

  return (
    <div className="min-h-screen bg-[#1a472a] text-white font-sans overflow-hidden flex flex-col relative">
      {/* Background Decoration */}
      <div className="absolute inset-0 pointer-events-none opacity-10 flex items-center justify-center overflow-hidden">
        <div className="rotate-12 translate-x-40 -translate-y-20">
          <PlayingCard isFaceDown />
        </div>
        <div className="-rotate-12 -translate-x-40 translate-y-20">
          <PlayingCard isFaceDown />
        </div>
      </div>

      {/* Header */}
      <header className="p-4 flex justify-between items-center z-10 bg-black/20 backdrop-blur-sm">
        <div className="flex items-center gap-3">
          <div className="bg-yellow-500 p-2 rounded-lg shadow-lg">
            <RotateCcw className="w-6 h-6 text-black cursor-pointer" onClick={initGame} />
          </div>
          <div>
            <h1 className="text-xl font-bold tracking-tight">汤小米.疯狂八点</h1>
            <p className="text-xs opacity-70">Crazy Eights Classic</p>
          </div>
        </div>
        
        <div className="flex gap-4 items-center">
          <div className="flex items-center gap-2 bg-white/10 px-3 py-1 rounded-full border border-white/20">
            <Cpu className="w-4 h-4" />
            <span className="text-sm font-mono">{gameState.aiHand.length}</span>
          </div>
          <div className="flex items-center gap-2 bg-white/10 px-3 py-1 rounded-full border border-white/20">
            <User className="w-4 h-4" />
            <span className="text-sm font-mono">{gameState.playerHand.length}</span>
          </div>
        </div>
      </header>

      {/* Main Game Area */}
      <main className="flex-1 flex flex-col justify-between p-4 relative z-10">
        
        {/* AI Hand */}
        <div className="flex justify-center h-24 sm:h-36">
          <div className="flex -space-x-8 sm:-space-x-12">
            {gameState.aiHand.map((card, i) => (
              <motion.div
                key={card.id}
                initial={{ y: -50, opacity: 0 }}
                animate={{ y: 0, opacity: 1 }}
                transition={{ delay: i * 0.05 }}
              >
                <PlayingCard isFaceDown className="shadow-2xl" />
              </motion.div>
            ))}
          </div>
        </div>

        {/* Center: Deck and Discard Pile */}
        <div className="flex justify-center items-center gap-8 sm:gap-16 my-8">
          {/* Draw Pile */}
          <div className="relative group">
            <div className="absolute -inset-1 bg-yellow-400/20 rounded-xl blur opacity-0 group-hover:opacity-100 transition duration-500" />
            <div 
              className={`relative cursor-pointer transition-transform active:scale-95 ${gameState.currentTurn !== 'player' ? 'opacity-50 grayscale' : ''}`}
              onClick={() => gameState.currentTurn === 'player' && drawCard(true)}
            >
              <PlayingCard isFaceDown />
              <div className="absolute -bottom-6 left-0 right-0 text-center text-[10px] uppercase tracking-widest font-bold opacity-60">
                摸牌 ({gameState.deck.length})
              </div>
            </div>
          </div>

          {/* Discard Pile */}
          <div className="relative">
            <AnimatePresence mode="wait">
              <motion.div
                key={topCard?.id}
                initial={{ scale: 0.8, opacity: 0, rotate: -10 }}
                animate={{ scale: 1, opacity: 1, rotate: 0 }}
                exit={{ scale: 1.2, opacity: 0, rotate: 10 }}
                className="relative"
              >
                <PlayingCard card={topCard} />
                {gameState.declaredSuit && (
                  <div className="absolute -top-4 -right-4 bg-white rounded-full p-2 shadow-lg border-2 border-yellow-400 animate-pulse">
                    <SuitIcon suit={gameState.declaredSuit} className="w-6 h-6" />
                  </div>
                )}
              </motion.div>
            </AnimatePresence>
            <div className="absolute -bottom-6 left-0 right-0 text-center text-[10px] uppercase tracking-widest font-bold opacity-60">
              弃牌堆
            </div>
          </div>
        </div>

        {/* Player Hand */}
        <div className="flex flex-col items-center gap-4">
          <div className="text-sm font-bold uppercase tracking-widest opacity-50 flex items-center gap-2">
            {gameState.currentTurn === 'player' ? (
              <span className="text-yellow-400 animate-pulse">你的回合</span>
            ) : (
              <span>AI 思考中...</span>
            )}
          </div>
          <div className="flex justify-center flex-wrap gap-2 sm:gap-4 max-w-4xl">
            {gameState.playerHand.map((card, i) => (
              <motion.div
                key={card.id}
                initial={{ y: 50, opacity: 0 }}
                animate={{ y: 0, opacity: 1 }}
                transition={{ delay: i * 0.05 }}
              >
                <PlayingCard 
                  card={card} 
                  isPlayable={gameState.currentTurn === 'player' && isCardPlayable(card)}
                  onClick={() => playCard(card, true)}
                />
              </motion.div>
            ))}
          </div>
        </div>
      </main>

      {/* Suit Selector Modal */}
      <AnimatePresence>
        {showSuitSelector && (
          <motion.div 
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            exit={{ opacity: 0 }}
            className="fixed inset-0 z-50 flex items-center justify-center bg-black/80 backdrop-blur-md p-4"
          >
            <motion.div 
              initial={{ scale: 0.9, y: 20 }}
              animate={{ scale: 1, y: 0 }}
              className="bg-[#1a472a] border-2 border-yellow-400 p-8 rounded-3xl shadow-2xl max-w-md w-full text-center"
            >
              <h2 className="text-2xl font-bold mb-2 text-yellow-400">疯狂 8 点!</h2>
              <p className="text-white/70 mb-8">请选择接下来要匹配的花色</p>
              
              <div className="grid grid-cols-2 gap-4">
                {[Suit.HEARTS, Suit.DIAMONDS, Suit.CLUBS, Suit.SPADES].map(suit => (
                  <button
                    key={suit}
                    onClick={() => declareSuit(suit)}
                    className="bg-white hover:bg-yellow-50 transition-colors p-6 rounded-2xl flex flex-col items-center gap-2 group shadow-lg"
                  >
                    <SuitIcon suit={suit} className="w-12 h-12 group-hover:scale-110 transition-transform" />
                    <span className="text-black font-bold uppercase text-xs tracking-widest">
                      {suit === Suit.HEARTS ? '红心' : suit === Suit.DIAMONDS ? '方块' : suit === Suit.CLUBS ? '梅花' : '黑桃'}
                    </span>
                  </button>
                ))}
              </div>
            </motion.div>
          </motion.div>
        )}
      </AnimatePresence>

      {/* Game Over Modal */}
      <AnimatePresence>
        {(gameState.status === 'player_won' || gameState.status === 'ai_won') && (
          <motion.div 
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            className="fixed inset-0 z-50 flex items-center justify-center bg-black/90 backdrop-blur-xl p-4"
          >
            <motion.div 
              initial={{ scale: 0.8, rotate: -5 }}
              animate={{ scale: 1, rotate: 0 }}
              className="bg-white text-black p-10 rounded-[2.5rem] shadow-2xl max-w-sm w-full text-center relative overflow-hidden"
            >
              <div className="absolute top-0 left-0 w-full h-2 bg-yellow-400" />
              
              <div className="mb-6 inline-block p-4 bg-yellow-100 rounded-full">
                <Trophy className={`w-12 h-12 ${gameState.status === 'player_won' ? 'text-yellow-600' : 'text-slate-400'}`} />
              </div>

              <h2 className="text-4xl font-black mb-2 tracking-tighter">
                {gameState.status === 'player_won' ? '你赢了!' : 'AI 获胜!'}
              </h2>
              <p className="text-slate-500 mb-8 font-medium">
                {gameState.status === 'player_won' ? '太棒了，你清空了所有手牌！' : '下次努力，AI 棋高一着。'}
              </p>

              <button
                onClick={initGame}
                className="w-full bg-black text-white py-4 rounded-2xl font-bold text-lg hover:bg-slate-800 transition-colors flex items-center justify-center gap-2 shadow-xl active:scale-95 transition-transform"
              >
                <RotateCcw className="w-5 h-5" />
                再玩一局
              </button>
            </motion.div>
          </motion.div>
        )}
      </AnimatePresence>

      {/* Footer Info */}
      <footer className="p-4 text-center text-[10px] opacity-30 uppercase tracking-[0.2em] font-bold">
        Crazy Eights Engine v1.0 • Built with React & Motion
      </footer>
    </div>
  );
}
