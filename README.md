import React, { useState, useEffect } from 'react';
import { User, Heart, Zap, Shield, ChevronRight, CheckCircle2, XCircle, RefreshCcw, Info, MessageCircle, ArrowRight } from 'lucide-react';

// --- PIXEL ART AVATAR COMPONENT ---
const PixelAleksey = ({ mood }) => {
  const getEyeColor = () => mood === 'sad' ? '#64748b' : mood === 'angry' ? '#ef4444' : '#1e293b';
  return (
    <svg width="120" height="120" viewBox="0 0 20 20" className="shape-rendering-crispEdges">
      {/* Hair */}
      <rect x="5" y="3" width="10" height="4" fill="#475569" />
      <rect x="4" y="4" width="2" height="4" fill="#475569" />
      {/* Face */}
      <rect x="6" y="5" width="8" height="8" fill="#f1f5f9" />
      <rect x="5" y="7" width="10" height="5" fill="#f1f5f9" />
      {/* Eyes */}
      <rect x="7" y="8" width="2" height="2" fill={getEyeColor()} />
      <rect x="11" y="8" width="2" height="2" fill={getEyeColor()} />
      {/* Mouth */}
      {mood === 'sad' ? (
        <rect x="8" y="11" width="4" height="1" fill="#94a3b8" />
      ) : mood === 'happy' ? (
        <path d="M8 11h4v1H8z" fill="#10b981" />
      ) : (
        <rect x="8" y="11" width="4" height="1" fill="#1e293b" />
      )}
      {/* Shirt */}
      <rect x="4" y="13" width="12" height="5" fill="#3b82f6" />
      <rect x="6" y="13" width="8" height="2" fill="#2563eb" />
    </svg>
  );
};

// --- SCENARIO DATA ---
const stepsData = [
  {
    id: 1,
    title: "Вход в контакт",
    character: "Алексей",
    mood: "neutral",
    context: "Алексей заходит, садится на край стула и смотрит в окно. 'Слушай, я не знаю, с чего начать. Вроде работаю много, а выхлопа ноль. Чувствую себя каким-то балластом в команде'.",
    options: [
      {
        type: "expert",
        text: "Взбодрить фактами: 'Брось, я смотрел твои коммиты за неделю, там нормальный объем. Ты точно не балласт, просто устал'.",
        trustChange: 5,
        autonomyChange: -5,
        feedback: "Роль Эксперта: Ты успокоил его, но он так и не понял, почему у него возникло это чувство. Ты 'залепил пластырем' его неуверенность."
      },
      {
        type: "rescuer",
        text: "Предложить помощь: 'Давай я на неделю заберу у тебя часть задач, чтобы ты выдохнул и пришел в себя?'.",
        trustChange: 15,
        autonomyChange: -20,
        feedback: "Роль Спасателя: Доверие выросло, но Автономия рухнула. Теперь он знает, что если станет грустно — ты сделаешь его работу за него."
      },
      {
        type: "coach",
        text: "Исследовать чувство: 'Ты сказал «балластом»... Что именно в твоих результатах заставляет тебя так думать?'.",
        trustChange: 10,
        autonomyChange: 10,
        isCorrect: true,
        feedback: "Роль Коуча: Ты не оцениваешь его чувства, а даешь ему инструмент для самоанализа. Это путь к автономии."
      }
    ]
  },
  {
    id: 2,
    title: "Механика: Эхо",
    character: "Алексей",
    mood: "sad",
    context: "Алексей вздыхает: 'Я просто запутался в приоритетах. Продакты приходят с горящими глазами, ты просишь архитектуру, а код в итоге получается кривой и медленный'. Какое слово ты 'отзеркалиш', чтобы он раскрыл корень проблемы?",
    isEcho: true,
    options: [
      {
        text: "'Кривой и медленный?'",
        trustChange: 5,
        autonomyChange: 5,
        feedback: "Ты сфокусировался на качестве кода. Это важно, но это симптом, а не причина."
      },
      {
        text: "'Запутался в приоритетах?'",
        trustChange: 10,
        autonomyChange: 15,
        isCorrect: true,
        feedback: "Эхо на приоритетах ведет к обсуждению процессов и его умения говорить 'нет'. Это стратегический уровень."
      },
      {
        text: "'Продакты приходят?'",
        trustChange: 5,
        autonomyChange: 0,
        feedback: "Это уведет разговор в жалобы на коллег. Ты рискуешь стать 'жилеткой' для слез."
      }
    ]
  },
  {
    id: 3,
    title: "Модель GROW: Goal",
    character: "Алексей",
    mood: "neutral",
    context: "Алексей признает, что не умеет распределять время. Тебе нужно перевести его из жалобы в цель. Что спросишь?",
    options: [
      {
        type: "expert",
        text: "«Как ты думаешь, техника Time Blocking поможет тебе решить эту проблему?»",
        trustChange: 0,
        autonomyChange: -10,
        feedback: "Закрытый вопрос-совет. Ты подсказал решение, лишив его возможности придумать свое."
      },
      {
        type: "rescuer",
        text: "«Давай я поговорю с продактами, чтобы они не дергали тебя до обеда?»",
        trustChange: 10,
        autonomyChange: -25,
        feedback: "Ты снова спасаешь. Алексей остается пассивным участником своей жизни."
      },
      {
        type: "coach",
        text: "«Если мы представим твой идеальный рабочий день через месяц — как он выглядит в плане задач?»",
        trustChange: 10,
        autonomyChange: 15,
        isCorrect: true,
        feedback: "Создание видения цели. Это классический этап Goal в GROW."
      }
    ]
  },
  {
    id: 4,
    title: "Работа с Реальностью",
    character: "Алексей",
    mood: "sad",
    context: "Алексей: 'Идеально — это когда я 4 часа пишу код без встреч. Но сейчас реальность такая, что я даже почту проверить не успеваю'. Как оценить масштаб проблемы?",
    options: [
      {
        type: "expert",
        text: "«Скинь мне свой календарь, я помогу тебе вычистить лишние встречи».",
        trustChange: 5,
        autonomyChange: -15,
        feedback: "Ты делаешь работу за него. Он не научится чистить календарь сам."
      },
      {
        type: "coach",
        text: "«Давай оценим по шкале от 1 до 10: насколько текущий график близок к твоему идеалу? И что мешает сделать его хотя бы на 1 балл выше?»",
        trustChange: 10,
        autonomyChange: 20,
        isCorrect: true,
        feedback: "Шкалирование превращает эмоции в данные. Теперь у него есть измеримая точка опоры."
      },
      {
        type: "rescuer",
        text: "«Это ужасно, я сам так мучился. Хочешь, перенесем наш 1-on-1 на следующую неделю, чтобы у тебя было время?»",
        trustChange: 15,
        autonomyChange: -10,
        feedback: "Ты уходишь от проблемы вместе с ним. Проблема не решена, прогресса нет."
      }
    ]
  },
  {
    id: 5,
    title: "Ловушка совета",
    character: "Алексей",
    mood: "neutral",
    context: "Алексей: 'Я не знаю, как отказать продакту. Он же старше меня по грейду. Как бы ты поступил на моем месте?'.",
    options: [
      {
        type: "expert",
        text: "«Я обычно говорю: 'Ок, я сделаю это, но тогда упадет вот эта задача'. Попробуй так».",
        trustChange: 5,
        autonomyChange: -20,
        feedback: "Ты дал готовый скрипт. Если он не сработает, он придет и скажет: 'Твой метод фигня'."
      },
      {
        type: "coach",
        text: "«У меня есть пара приемов, но давай сначала подумаем: какие у тебя есть сильные аргументы, чтобы защитить свое время?»",
        trustChange: 5,
        autonomyChange: 15,
        isCorrect: true,
        feedback: "Ты подтвердил свою роль ментора, но оставил право первого хода за ним."
      },
      {
        type: "rescuer",
        text: "«Да просто игнорируй его, если это не срочно. Я прикрою тебя перед руководством».",
        trustChange: 15,
        autonomyChange: -30,
        feedback: "Очень опасно. Ты поощряешь конфликтное поведение и полную зависимость от твоей защиты."
      }
    ]
  },
  {
    id: 6,
    title: "Скрытая эмоция",
    character: "Алексей",
    mood: "angry",
    context: "Внезапно Алексей злится: 'Да вообще, почему я должен об этом думать? Я инженер, а не менеджер! Бесит всё!'.",
    options: [
      {
        type: "expert",
        text: "«Спокойно. Гнев не поможет делу. Давай вернемся к фактам».",
        trustChange: -20,
        autonomyChange: 0,
        feedback: "Подавление эмоций разрушает доверие. Ты запретил ему чувствовать."
      },
      {
        type: "coach",
        text: "«Вижу, тебя это действительно задевает. Что именно в необходимости управлять своим временем вызывает такой протест?»",
        trustChange: 10,
        autonomyChange: 10,
        isCorrect: true,
        feedback: "Валидация эмоций. Ты признал гнев и направил его энергию в конструктивное русло."
      },
      {
        type: "rescuer",
        text: "«Да, менеджмент — это отстой. Я сам ненавижу эти таблицы. Понимаю тебя как никто».",
        trustChange: 20,
        autonomyChange: -10,
        feedback: "Ты присоединился к его 'жертве'. Теперь вы вдвоем против системы, но работать лучше он не станет."
      }
    ]
  },
  {
    id: 7,
    title: "Варианты (Options)",
    character: "Алексей",
    mood: "neutral",
    context: "Эмоции улеглись. Алексей: 'Ну, я мог бы ставить слоты в календаре... Или просить тебя фильтровать входящие... Что еще можно сделать?'.",
    options: [
      {
        type: "expert",
        text: "«Можешь еще почитать книгу 'Эссенциализм', там классные советы по отказам».",
        trustChange: 0,
        autonomyChange: -5,
        feedback: "Слабо. Ты отправил его читать книгу вместо того, чтобы вытащить идеи из его головы."
      },
      {
        type: "coach",
        text: "«А если бы ты был на моем месте и тебе нужно было помочь коллеге в такой ситуации — что бы ты предложил?»",
        trustChange: 5,
        autonomyChange: 20,
        isCorrect: true,
        feedback: "Смена перспективы. Это 'взламывает' мозг и заставляет видеть неочевидные варианты."
      },
      {
        type: "rescuer",
        text: "«Давай я буду каждое утро проверять твой список задач и говорить, что делать первым».",
        trustChange: 5,
        autonomyChange: -40,
        feedback: "Это смерть автономии. Ты стал его личным секретарем. Поздравляю с выгоранием!"
      }
    ]
  },
  {
    id: 8,
    title: "Обратная связь (SBI)",
    character: "Тимлид (Ты)",
    mood: "neutral",
    context: "Настало время сказать про заваленный дедлайн во вторник. Как сделать это коучингово?",
    options: [
      {
        type: "expert",
        text: "«Во вторник ты задержал задачу на 5 часов. Это недопустимо. Пожалуйста, будь внимательнее».",
        trustChange: -10,
        autonomyChange: 5,
        feedback: "Критика. Честно, но не развивает. Человек просто боится ошибиться снова."
      },
      {
        type: "coach",
        text: "«Во вторник дедлайн был пропущен, и QA не смогли начать работу. Как это событие соотносится с твоей целью 'стать автономным профи'?»",
        trustChange: 5,
        autonomyChange: 20,
        isCorrect: true,
        feedback: "Ты привязал конкретный факт к ЕГО ценностям и целям. Теперь он сам хочет это исправить."
      },
      {
        type: "rescuer",
        text: "«Слушай, там во вторник косяк был со сроками... Но я сказал всем, что это из-за багов в API, так что не переживай».",
        trustChange: 15,
        autonomyChange: -20,
        feedback: "Ты соврал, чтобы его защитить. Он усвоил: косячить можно, босс прикроет."
      }
    ]
  },
  {
    id: 9,
    title: "Проверка на коммитмент",
    character: "Алексей",
    mood: "happy",
    context: "Алексей: 'Слушай, спасибо. Я попробую на следующей неделе ставить блоки в календаре и говорить 'нет' продактам'. Твое действие?",
    options: [
      {
        type: "rescuer",
        text: "«Молодец! У тебя всё получится, я в тебя верю. Удачи!».",
        trustChange: 10,
        autonomyChange: 0,
        feedback: "Просто поддержка. Вероятность выполнения 'попробую' — 20%."
      },
      {
        type: "expert",
        text: "«Договорились. В пятницу проверю твой календарь».",
        trustChange: -5,
        autonomyChange: 5,
        feedback: "Жесткий контроль. Возвращает ощущение 'школьник-учитель'."
      },
      {
        type: "coach",
        text: "«Ты сказал 'попробую'... Что может помешать тебе сделать это на 100%? И как ты поймешь в конце недели, что план сработал?»",
        trustChange: 5,
        autonomyChange: 20,
        isCorrect: true,
        feedback: "Проверка на реалистичность. Ты переводишь 'попробую' в 'сделаю'."
      }
    ]
  },
  {
    id: 10,
    title: "Финал",
    character: "Алексей",
    mood: "happy",
    context: "Алексей встает, пожимает тебе руку. 'Хорошо поговорили. Неожиданно. Я пошел кодить'. Что в твоей голове?",
    options: [
      {
        type: "expert",
        text: "«Надеюсь, он всё понял. Если нет — придется самому всё переделывать».",
        trustChange: 0,
        autonomyChange: -10,
        feedback: "Ты всё еще не веришь в него. Это транслируется через микросигналы."
      },
      {
        type: "coach",
        text: "«Он сам нашел 3 решения. Моя задача теперь — просто не мешать ему пробовать».",
        trustChange: 10,
        autonomyChange: 15,
        isCorrect: true,
        feedback: "Истинная позиция коуча. Ты доверяешь процессу и человеку."
      },
      {
        type: "rescuer",
        text: "«Надо будет ему вечером написать, поддержать, а то вдруг он опять расстроится».",
        trustChange: 5,
        autonomyChange: -15,
        feedback: "Тревожная опека. Ты не даешь ему шанса набить свои шишки."
      }
    ]
  }
];

export default function App() {
  const [step, setStep] = useState(-1);
  const [trust, setTrust] = useState(50);
  const [autonomy, setAutonomy] = useState(30);
  const [selected, setSelected] = useState(null);
  const [showFeedback, setShowFeedback] = useState(false);

  const currentData = stepsData[step];

  const handleSelect = (option) => {
    if (showFeedback) return;
    setSelected(option);
    setShowFeedback(true);
    setTrust(t => Math.max(0, Math.min(100, t + option.trustChange)));
    setAutonomy(a => Math.max(0, Math.min(100, a + option.autonomyChange)));
  };

  const nextStep = () => {
    setShowFeedback(false);
    setSelected(null);
    setStep(s => s + 1);
  };

  const restart = () => {
    setStep(-1);
    setTrust(50);
    setAutonomy(30);
    setShowFeedback(false);
  };

  if (step === -1) {
    return (
      <div className="min-h-screen bg-white text-slate-900 flex items-center justify-center p-6 font-sans">
        <div className="max-w-md w-full text-center">
          <div className="flex justify-center mb-8">
            <PixelAleksey mood="neutral" />
          </div>
          <h1 className="text-4xl font-black mb-4 tracking-tighter text-blue-600">КЛУБ ТИМЛИДОВ 3.0</h1>
          <p className="text-lg text-slate-500 mb-10 leading-relaxed">
            Научись проводить 1-on-1 в коучинговом стиле. <br/>
            Сбалансируй <b>Доверие</b> и <b>Автономию</b>. 
            Если Доверие упадет до 0 — Алексей уйдет. Если Автономия будет 0 — ты сгоришь.
          </p>
          <button 
            onClick={nextStep}
            className="w-full bg-blue-600 hover:bg-blue-700 text-white py-4 rounded-xl font-bold text-lg shadow-xl shadow-blue-200 transition-all active:scale-95"
          >
            Начать сессию
          </button>
        </div>
      </div>
    );
  }

  if (step >= stepsData.length) {
    return (
      <div className="min-h-screen bg-white text-slate-900 flex items-center justify-center p-6">
        <div className="max-w-md w-full text-center">
          <h2 className="text-3xl font-black mb-8">Итоги сессии</h2>
          <div className="space-y-6 mb-10">
            <div className="flex justify-between items-center bg-slate-50 p-6 rounded-2xl border border-slate-100">
              <span className="font-bold text-slate-500">Доверие:</span>
              <span className={`text-3xl font-black ${trust > 50 ? 'text-blue-600' : 'text-red-500'}`}>{trust}%</span>
            </div>
            <div className="flex justify-between items-center bg-slate-50 p-6 rounded-2xl border border-slate-100">
              <span className="font-bold text-slate-500">Автономия:</span>
              <span className={`text-3xl font-black ${autonomy > 50 ? 'text-blue-600' : 'text-orange-500'}`}>{autonomy}%</span>
            </div>
          </div>
          <p className="text-slate-600 mb-10 text-lg">
            {trust < 20 ? "Алексей уволился. Ты был слишком холодным экспертом." : 
             autonomy < 30 ? "Алексей счастлив, но теперь ты работаешь за двоих. Ты — Спасатель, а не лидер." :
             (trust > 60 && autonomy > 60) ? "Идеально! Ты настоящий коуч-лидер. Алексей растет, а у тебя есть время на стратегию." :
             "Неплохой результат, но ты часто сваливался в роль советчика. Попробуй еще раз!"}
          </p>
          <button onClick={restart} className="flex items-center justify-center gap-2 w-full border-2 border-blue-600 text-blue-600 py-4 rounded-xl font-bold hover:bg-blue-50 transition-all">
            <RefreshCcw size={20} /> Пройти заново
          </button>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-white text-slate-900 font-sans flex flex-col">
      {/* HEADER */}
      <header className="border-b border-slate-100 p-4 sticky top-0 bg-white/80 backdrop-blur-md z-50">
        <div className="max-w-2xl mx-auto flex justify-between items-center">
          <div className="flex gap-4">
            <div className="flex flex-col">
              <div className="flex items-center gap-1 mb-1">
                <Heart size={14} className="text-blue-600" />
                <span className="text-[10px] font-black uppercase text-slate-400">Доверие</span>
              </div>
              <div className="w-24 h-1.5 bg-slate-100 rounded-full overflow-hidden">
                <div className="h-full bg-blue-600 transition-all duration-500" style={{width: `${trust}%`}} />
              </div>
            </div>
            <div className="flex flex-col">
              <div className="flex items-center gap-1 mb-1">
                <Zap size={14} className="text-orange-500" />
                <span className="text-[10px] font-black uppercase text-slate-400">Автономия</span>
              </div>
              <div className="w-24 h-1.5 bg-slate-100 rounded-full overflow-hidden">
                <div className="h-full bg-orange-500 transition-all duration-500" style={{width: `${autonomy}%`}} />
              </div>
            </div>
          </div>
          <div className="text-[10px] font-black bg-blue-50 text-blue-600 px-3 py-1 rounded-full uppercase">
            Шаг {step + 1} / 10
          </div>
        </div>
      </header>

      {/* GAME AREA */}
      <main className="flex-1 flex flex-col items-center justify-center p-6">
        <div className="max-w-2xl w-full">
          <div className="flex flex-col items-center mb-10 text-center">
            <PixelAleksey mood={currentData.mood} />
            <div className="mt-4">
              <span className="text-xs font-black text-blue-600 uppercase tracking-widest block mb-1">{currentData.character}</span>
              <h3 className="text-2xl md:text-3xl font-medium leading-tight text-slate-800">
                «{currentData.context}»
              </h3>
            </div>
          </div>

          <div className="grid gap-3">
            {currentData.options.map((opt, i) => (
              <button
                key={i}
                onClick={() => handleSelect(opt)}
                disabled={showFeedback}
                className={`
                  w-full p-5 rounded-2xl border-2 text-left transition-all duration-200 group
                  ${!showFeedback 
                    ? 'border-slate-100 bg-white hover:border-blue-600 hover:shadow-lg hover:shadow-blue-50' 
                    : (selected === opt ? (opt.isCorrect ? 'border-blue-600 bg-blue-50' : 'border-slate-300 opacity-60') : 'border-slate-50 opacity-30')}
                `}
              >
                <div className="flex items-center gap-4">
                  <div className={`w-2 h-2 rounded-full ${!showFeedback ? 'bg-slate-200 group-hover:bg-blue-600' : (opt.isCorrect ? 'bg-blue-600' : 'bg-slate-400')}`} />
                  <span className="text-slate-700 font-medium">{opt.text}</span>
                </div>
              </button>
            ))}
          </div>
        </div>
      </main>

      {/* FEEDBACK DRAWER */}
      {showFeedback && (
        <div className="fixed bottom-0 left-0 right-0 bg-white border-t-4 border-blue-600 p-8 shadow-2xl z-[100] animate-in slide-in-from-bottom-full duration-300">
          <div className="max-w-2xl mx-auto flex flex-col md:flex-row items-center gap-8">
            <div className="flex-1">
              <div className="flex items-center gap-2 mb-3">
                <div className={`p-1 rounded ${selected.isCorrect ? 'bg-blue-100 text-blue-600' : 'bg-slate-100 text-slate-500'}`}>
                  {selected.isCorrect ? <CheckCircle2 size={18} /> : <Info size={18} />}
                </div>
                <span className="font-black uppercase text-xs tracking-widest text-slate-400">Разбор полетов</span>
              </div>
              <p className="text-slate-800 text-lg leading-relaxed">{selected.feedback}</p>
            </div>
            <button 
              onClick={nextStep}
              className="w-full md:w-auto bg-blue-600 text-white px-12 py-4 rounded-xl font-bold flex items-center justify-center gap-2 hover:bg-blue-700 active:scale-95 transition-all"
            >
              Дальше <ArrowRight size={20} />
            </button>
          </div>
        </div>
      )}
    </div>
  );
}
