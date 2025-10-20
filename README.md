<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Matrice Priorisation Kenya</title>
    <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body>
    <div id="root"></div>
    
    <script type="text/babel">
       import React, { useState } from 'react';
import { Download, Plus, Trash2, Info } from 'lucide-react';

const PrioritizationMatrix = () => {
  const [useCases, setUseCases] = useState([
    { id: 1, name: 'Exemple: Détection fraude fiscale', scores: {} }
  ]);
  
  const criteria = [
    {
      category: 'IMPACT',
      items: [
        { id: 'impact_citizens', name: 'Impact citoyen', desc: 'Nombre de bénéficiaires directs', weight: 20 },
        { id: 'impact_economy', name: 'Impact économique', desc: 'Économies ou revenus générés', weight: 15 },
        { id: 'alignment_strategy', name: 'Alignement stratégique', desc: 'Contribution aux priorités gouvernementales', weight: 15 }
      ]
    },
    {
      category: 'FAISABILITÉ',
      items: [
        { id: 'data_availability', name: 'Disponibilité données', desc: 'Qualité et accessibilité des données', weight: 10 },
        { id: 'technical_complexity', name: 'Complexité technique', desc: 'Simplicité de mise en œuvre (inversé)', weight: 10 },
        { id: 'implementation_time', name: 'Temps de mise en œuvre', desc: 'Rapidité de déploiement (inversé)', weight: 8 }
      ]
    },
    {
      category: 'RESSOURCES',
      items: [
        { id: 'budget', name: 'Coût', desc: 'Budget requis (inversé)', weight: 10 },
        { id: 'skills', name: 'Compétences disponibles', desc: 'Capacités internes existantes', weight: 7 },
        { id: 'infrastructure', name: 'Infrastructure', desc: 'Systèmes et technologies en place', weight: 5 }
      ]
    }
  ];
  
  const [showInfo, setShowInfo] = useState({});

  const addUseCase = () => {
    const newId = Math.max(...useCases.map(uc => uc.id), 0) + 1;
    setUseCases([...useCases, { id: newId, name: `Cas d'usage ${newId}`, scores: {} }]);
  };

  const removeUseCase = (id) => {
    setUseCases(useCases.filter(uc => uc.id !== id));
  };

  const updateName = (id, name) => {
    setUseCases(useCases.map(uc => uc.id === id ? { ...uc, name } : uc));
  };

  const updateScore = (ucId, criterionId, value) => {
    setUseCases(useCases.map(uc => 
      uc.id === ucId 
        ? { ...uc, scores: { ...uc.scores, [criterionId]: parseInt(value) || 0 } }
        : uc
    ));
  };

  const calculateTotal = (useCase) => {
    let total = 0;
    criteria.forEach(cat => {
      cat.items.forEach(item => {
        const score = useCase.scores[item.id] || 0;
        total += (score * item.weight) / 5;
      });
    });
    return Math.round(total);
  };

  const exportToCSV = () => {
    let csv = 'Cas d\'usage,';
    criteria.forEach(cat => {
      cat.items.forEach(item => {
        csv += `${item.name} (${item.weight}%),`;
      });
    });
    csv += 'Score Total\n';

    useCases.forEach(uc => {
      csv += `${uc.name},`;
      criteria.forEach(cat => {
        cat.items.forEach(item => {
          csv += `${uc.scores[item.id] || 0},`;
        });
      });
      csv += `${calculateTotal(uc)}\n`;
    });

    const blob = new Blob([csv], { type: 'text/csv' });
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'matrice_priorisation_kenya.csv';
    a.click();
  };

  const rankedUseCases = [...useCases].sort((a, b) => calculateTotal(b) - calculateTotal(a));

  return (
    <div className="min-h-screen bg-gradient-to-br from-slate-50 to-slate-100 p-6">
      <div className="max-w-7xl mx-auto">
        <div className="bg-white rounded-lg shadow-lg p-6 mb-6">
          <div className="flex justify-between items-start mb-4">
            <div>
              <h1 className="text-3xl font-bold text-slate-800 mb-2">
                Matrice de Priorisation - Cas d'Usage IA
              </h1>
              <p className="text-slate-600">Gouvernement du Kenya - Exploitation de données</p>
            </div>
            <div className="flex gap-2">
              <button
                onClick={addUseCase}
                className="flex items-center gap-2 bg-emerald-600 text-white px-4 py-2 rounded-lg hover:bg-emerald-700 transition"
              >
                <Plus size={20} />
                Ajouter
              </button>
              <button
                onClick={exportToCSV}
                className="flex items-center gap-2 bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition"
              >
                <Download size={20} />
                Exporter
              </button>
            </div>
          </div>

          <div className="bg-amber-50 border-l-4 border-amber-500 p-4 mb-6">
            <p className="text-sm text-amber-800">
              <strong>Notation:</strong> Évaluez chaque critère de 1 (faible) à 5 (excellent). 
              Le score total est calculé automatiquement en pondérant chaque critère.
            </p>
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full bg-white rounded-lg shadow-lg">
            <thead>
              <tr className="bg-slate-800 text-white">
                <th className="p-4 text-left sticky left-0 bg-slate-800 z-10 min-w-[200px]">
                  Cas d'Usage
                </th>
                {criteria.map(cat => (
                  <React.Fragment key={cat.category}>
                    <th colSpan={cat.items.length} className="p-4 text-center border-l-2 border-slate-600">
                      {cat.category}
                    </th>
                  </React.Fragment>
                ))}
                <th className="p-4 text-center border-l-2 border-slate-600 min-w-[120px]">
                  SCORE TOTAL
                </th>
                <th className="p-4 text-center border-l-2 border-slate-600">
                  Actions
                </th>
              </tr>
              <tr className="bg-slate-700 text-white text-sm">
                <th className="p-3 sticky left-0 bg-slate-700 z-10"></th>
                {criteria.map(cat => (
                  cat.items.map(item => (
                    <th key={item.id} className="p-3 text-center relative">
                      <div className="flex items-center justify-center gap-1">
                        <span>{item.name}</span>
                        <button
                          onMouseEnter={() => setShowInfo({...showInfo, [item.id]: true})}
                          onMouseLeave={() => setShowInfo({...showInfo, [item.id]: false})}
                          className="relative"
                        >
                          <Info size={14} />
                          {showInfo[item.id] && (
                            <div className="absolute top-full mt-2 left-1/2 transform -translate-x-1/2 bg-slate-900 text-white text-xs p-2 rounded shadow-lg w-48 z-20">
                              {item.desc}
                            </div>
                          )}
                        </button>
                      </div>
                      <div className="text-amber-400 font-bold">({item.weight}%)</div>
                    </th>
                  ))
                ))}
                <th className="p-3"></th>
                <th className="p-3"></th>
              </tr>
            </thead>
            <tbody>
              {useCases.map((uc, idx) => (
                <tr key={uc.id} className={idx % 2 === 0 ? 'bg-slate-50' : 'bg-white'}>
                  <td className="p-3 sticky left-0 z-10 bg-inherit">
                    <input
                      type="text"
                      value={uc.name}
                      onChange={(e) => updateName(uc.id, e.target.value)}
                      className="w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-emerald-500 focus:outline-none"
                    />
                  </td>
                  {criteria.map(cat => (
                    cat.items.map(item => (
                      <td key={item.id} className="p-3 text-center">
                        <select
                          value={uc.scores[item.id] || 0}
                          onChange={(e) => updateScore(uc.id, item.id, e.target.value)}
                          className="w-20 p-2 border border-slate-300 rounded focus:ring-2 focus:ring-emerald-500 focus:outline-none"
                        >
                          <option value="0">-</option>
                          <option value="1">1</option>
                          <option value="2">2</option>
                          <option value="3">3</option>
                          <option value="4">4</option>
                          <option value="5">5</option>
                        </select>
                      </td>
                    ))
                  ))}
                  <td className="p-3 text-center border-l-2 border-slate-300">
                    <div className="text-2xl font-bold text-emerald-700">
                      {calculateTotal(uc)}/100
                    </div>
                  </td>
                  <td className="p-3 text-center border-l-2 border-slate-300">
                    <button
                      onClick={() => removeUseCase(uc.id)}
                      className="text-red-600 hover:text-red-800 p-2"
                    >
                      <Trash2 size={20} />
                    </button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>

        <div className="mt-8 bg-white rounded-lg shadow-lg p-6">
          <h2 className="text-2xl font-bold text-slate-800 mb-4">Classement des Cas d'Usage</h2>
          <div className="space-y-3">
            {rankedUseCases.map((uc, idx) => {
              const score = calculateTotal(uc);
              const barColor = score >= 70 ? 'bg-emerald-500' : score >= 50 ? 'bg-amber-500' : 'bg-red-500';
              
              return (
                <div key={uc.id} className="flex items-center gap-4">
                  <div className="text-2xl font-bold text-slate-600 w-8">
                    #{idx + 1}
                  </div>
                  <div className="flex-1">
                    <div className="flex justify-between mb-1">
                      <span className="font-semibold text-slate-800">{uc.name}</span>
                      <span className="font-bold text-slate-700">{score}/100</span>
                    </div>
                    <div className="w-full bg-slate-200 rounded-full h-4">
                      <div
                        className={`${barColor} h-4 rounded-full transition-all duration-500`}
                        style={{ width: `${score}%` }}
                      ></div>
                    </div>
                  </div>
                </div>
              );
            })}
          </div>
        </div>

        <div className="mt-6 bg-white rounded-lg shadow-lg p-6">
          <h3 className="text-xl font-bold text-slate-800 mb-3">Guide d'Interprétation</h3>
          <div className="grid md:grid-cols-3 gap-4 text-sm">
            <div className="p-4 bg-emerald-50 rounded-lg border-l-4 border-emerald-500">
              <div className="font-bold text-emerald-800 mb-2">Score ≥ 70 - PRIORITÉ HAUTE</div>
              <p className="text-slate-700">Fort potentiel d'impact avec faisabilité élevée. À lancer rapidement.</p>
            </div>
            <div className="p-4 bg-amber-50 rounded-lg border-l-4 border-amber-500">
              <div className="font-bold text-amber-800 mb-2">Score 50-69 - PRIORITÉ MOYENNE</div>
              <p className="text-slate-700">Potentiel intéressant mais nécessite des ajustements ou ressources supplémentaires.</p>
            </div>
            <div className="p-4 bg-red-50 rounded-lg border-l-4 border-red-500">
              <div className="font-bold text-red-800 mb-2">Score &lt; 50 - PRIORITÉ BASSE</div>
              <p className="text-slate-700">À reconsidérer ou reporter. Impact limité ou contraintes trop importantes.</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
};

export default PrioritizationMatrix;
        ReactDOM.render(<PrioritizationMatrix />, document.getElementById('root'));
    </script>
</body>
</html>
