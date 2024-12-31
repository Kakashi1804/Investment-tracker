import React, { useState, useEffect } from 'react';
import { Card } from '@/components/ui/card';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { Textarea } from '@/components/ui/textarea';
import { LineChart, Line, XAxis, YAxis, Tooltip, Legend, ResponsiveContainer } from 'recharts';
import { Plus, Trash2, Edit2, Save, X } from 'lucide-react';

const CryptoTracker = () => {
  const YEARLY_GOAL = 5000;
  const WEEKLY_TARGET = YEARLY_GOAL / 52;

  const [entries, setEntries] = useState([]);
  const [currentEntry, setCurrentEntry] = useState({
    week: 1,
    investment: 0,
    netWorth: 0,
    rewards: 0,
    comments: '',
    assets: {},
    date: new Date().toISOString().split('T')[0]
  });

  const [newAsset, setNewAsset] = useState({
    name: '',
    amount: ''
  });

  const [editingAsset, setEditingAsset] = useState(null);
  const [editAmount, setEditAmount] = useState('');

  // Load data on mount
  useEffect(() => {
    const saved = localStorage.getItem('portfolioData');
    if (saved) {
      const parsed = JSON.parse(saved);
      setEntries(parsed);
      
      // Set up next entry with previous week's assets
      if (parsed.length > 0) {
        const lastEntry = parsed[parsed.length - 1];
        setCurrentEntry(prev => ({
          ...prev,
          week: parsed.length + 1,
          assets: { ...lastEntry.assets } // Copy previous week's assets
        }));
      }
    }
  }, []);

  // Save data when entries change
  useEffect(() => {
    localStorage.setItem('portfolioData', JSON.stringify(entries));
  }, [entries]);

  const handleSave = () => {
    setEntries(prev => [...prev, currentEntry]);
    setCurrentEntry({
      week: currentEntry.week + 1,
      investment: 0,
      netWorth: 0,
      rewards: 0,
      comments: '',
      assets: { ...currentEntry.assets }, // Carry over assets to next week
      date: new Date().toISOString().split('T')[0]
    });
  };

  const addAsset = () => {
    if (newAsset.name && newAsset.amount) {
      setCurrentEntry(prev => ({
        ...prev,
        assets: {
          ...prev.assets,
          [newAsset.name]: parseFloat(newAsset.amount)
        }
      }));
      setNewAsset({ name: '', amount: '' });
    }
  };

  const removeAsset = (assetName) => {
    const newAssets = { ...currentEntry.assets };
    delete newAssets[assetName];
    setCurrentEntry(prev => ({
      ...prev,
      assets: newAssets
    }));
  };

  const startEditAsset = (name, amount) => {
    setEditingAsset(name);
    setEditAmount(amount.toString());
  };

  const saveEditAsset = () => {
    if (editingAsset && editAmount) {
      setCurrentEntry(prev => ({
        ...prev,
        assets: {
          ...prev.assets,
          [editingAsset]: parseFloat(editAmount)
        }
      }));
      setEditingAsset(null);
      setEditAmount('');
    }
  };

  // Calculate progress
  const totalInvested = entries.reduce((sum, entry) => sum + entry.investment, 0);
  const progressPercent = (totalInvested / YEARLY_GOAL) * 100;
  const remaining = YEARLY_GOAL - totalInvested;

  // Calculate week-over-week net worth change
  const getNetWorthChange = () => {
    if (entries.length === 0) return null;
    const lastEntry = entries[entries.length - 1];
    if (entries.length === 1) return null;
    const previousEntry = entries[entries.length - 2];
    const change = ((lastEntry.netWorth - previousEntry.netWorth) / previousEntry.netWorth) * 100;
    return change;
  };

  return (
    <div className="p-6 max-w-4xl mx-auto space-y-6">
      {/* Progress Section */}
      <Card className="p-6">
        <h2 className="text-xl font-bold mb-4">Investment Progress</h2>
        <div className="space-y-4">
          <div className="h-4 bg-gray-200 rounded-full overflow-hidden">
            <div 
              className="h-full bg-blue-500 transition-all"
              style={{ width: `${Math.min(progressPercent, 100)}%` }}
            />
          </div>
          <div className="flex justify-between text-sm">
            <span>${totalInvested.toFixed(2)} invested</span>
            <span>${remaining.toFixed(2)} remaining</span>
          </div>
          <div className="p-4 bg-blue-50 rounded-lg">
            <p className="text-sm">
              Weekly Target: ${WEEKLY_TARGET.toFixed(2)}
            </p>
            {getNetWorthChange() !== null && (
              <p className={`text-sm mt-2 ${getNetWorthChange() >= 0 ? 'text-green-600' : 'text-red-600'}`}>
                Net Worth Change: {getNetWorthChange().toFixed(2)}%
              </p>
            )}
          </div>
        </div>
      </Card>

      {/* Input Section */}
      <Card className="p-6">
        <h2 className="text-xl font-bold mb-4">Week {currentEntry.week} Entry</h2>
        <div className="grid gap-4">
          <div>
            <label className="block text-sm font-medium mb-1">Date</label>
            <Input
              type="date"
              value={currentEntry.date}
              onChange={e => setCurrentEntry(prev => ({
                ...prev,
                date: e.target.value
              }))}
            />
          </div>
          <div>
            <label className="block text-sm font-medium mb-1">Investment Amount</label>
            <Input
              type="number"
              value={currentEntry.investment}
              onChange={e => setCurrentEntry(prev => ({
                ...prev,
                investment: parseFloat(e.target.value) || 0
              }))}
            />
          </div>
          <div>
            <label className="block text-sm font-medium mb-1">Net Worth</label>
            <Input
              type="number"
              value={currentEntry.netWorth}
              onChange={e => setCurrentEntry(prev => ({
                ...prev,
                netWorth: parseFloat(e.target.value) || 0
              }))}
            />
          </div>
          <div>
            <label className="block text-sm font-medium mb-1">Rewards</label>
            <Input
              type="number"
              value={currentEntry.rewards}
              onChange={e => setCurrentEntry(prev => ({
                ...prev,
                rewards: parseFloat(e.target.value) || 0
              }))}
            />
          </div>

          {/* Asset Allocation Section */}
          <div className="space-y-4 p-4 bg-green-50 rounded-lg">
            <h3 className="font-medium">Asset Allocation</h3>
            <div className="flex gap-2">
              <Input
                placeholder="Asset Name"
                value={newAsset.name}
                onChange={e => setNewAsset(prev => ({ ...prev, name: e.target.value }))}
              />
              <Input
                type="number"
                placeholder="Amount"
                value={newAsset.amount}
                onChange={e => setNewAsset(prev => ({ ...prev, amount: e.target.value }))}
              />
              <Button onClick={addAsset}>
                <Plus className="h-4 w-4" />
              </Button>
            </div>
            <div className="space-y-2">
              {Object.entries(currentEntry.assets).map(([name, amount]) => (
                <div key={name} className="flex justify-between items-center p-2 bg-white rounded">
                  <span>{name}</span>
                  <div className="flex items-center gap-2">
                    {editingAsset === name ? (
                      <>
                        <Input
                          type="number"
                          value={editAmount}
                          onChange={e => setEditAmount(e.target.value)}
                          className="w-24"
                        />
                        <Button
                          variant="ghost"
                          size="sm"
                          onClick={saveEditAsset}
                        >
                          <Save className="h-4 w-4 text-green-500" />
                        </Button>
                        <Button
                          variant="ghost"
                          size="sm"
                          onClick={() => setEditingAsset(null)}
                        >
                          <X className="h-4 w-4 text-red-500" />
                        </Button>
                      </>
                    ) : (
                      <>
                        <span>{amount}</span>
                        <Button
                          variant="ghost"
                          size="sm"
                          onClick={() => startEditAsset(name, amount)}
                        >
                          <Edit2 className="h-4 w-4 text-blue-500" />
                        </Button>
                        <Button
                          variant="ghost"
                          size="sm"
                          onClick={() => removeAsset(name)}
                        >
                          <Trash2 className="h-4 w-4 text-red-500" />
                        </Button>
                      </>
                    )}
                  </div>
                </div>
              ))}
            </div>
          </div>

          {/* Comments Section */}
          <div>
            <label className="block text-sm font-medium mb-1">Comments</label>
            <Textarea
              placeholder="Add notes about trades, escrow, or other important events..."
              value={currentEntry.comments}
              onChange={e => setCurrentEntry(prev => ({
                ...prev,
                comments: e.target.value
              }))}
              className="h-24"
            />
          </div>

          <Button 
            onClick={handleSave}
            className="w-full mt-4"
          >
            Save Week {currentEntry.week}
          </Button>
        </div>
      </Card>

      {/* Chart Section */}
      {entries.length > 0 && (
        <Card className="p-6">
          <h2 className="text-xl font-bold mb-4">Portfolio Performance</h2>
          <div className="h-[300px] w-full">
            <ResponsiveContainer>
              <LineChart data={entries}>
                <XAxis dataKey="week" />
                <YAxis />
                <Tooltip />
                <Legend />
                <Line 
                  type="monotone" 
                  dataKey="netWorth" 
                  stroke="#2563eb" 
                  name="Net Worth" 
                />
                <Line 
                  type="monotone" 
                  dataKey="investment" 
                  stroke="#16a34a" 
                  name="Investment" 
                />
                <Line 
                  type="monotone" 
                  dataKey="rewards" 
                  stroke="#9333ea" 
                  name="Rewards" 
                />
              </LineChart>
            </ResponsiveContainer>
          </div>
        </Card>
      )}

      {/* History Table */}
      {entries.length > 0 && (
        <Card className="p-6">
          <h2 className="text-xl font-bold mb-4">History</h2>
          <div className="overflow-x-auto">
            <table className="w-full">
              <thead>
                <tr>
                  <th className="text-left p-2">Week</th>
                  <th className="text-left p-2">Date</th>
                  <th className="text-right p-2">Investment</th>
                  <th className="text-right p-2">Net Worth</th>
                  <th className="text-right p-2">Rewards</th>
                  <th className="text-left p-2">Comments</th>
                </tr>
              </thead>
              <tbody>
                {entries.map((entry) => (
                  <tr key={entry.week} className="border-t hover:bg-gray-50">
                    <td className="p-2">Week {entry.week}</td>
                    <td className="p-2">{entry.date}</td>
                    <td className="text-right p-2">${entry.investment.toFixed(2)}</td>
                    <td className="text-right p-2">${entry.netWorth.toFixed(2)}</td>
                    <td className="text-right p-2">${entry.rewards.toFixed(2)}</td>
                    <td className="p-2 truncate max-w-xs">{entry.comments}</td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </Card>
      )}
    </div>
  );
};

export default CryptoTracker;
