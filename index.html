import { useState, useEffect, useRef, useCallback } from "react";

const STEPS = 16;
const DEFAULT_BPM = 90;

const PADS = [
  { id: "kick",  label: "KICK",   color: "#FF3B30", freq: 60,   type: "kick"  },
  { id: "snare", label: "SNARE",  color: "#FF9500", freq: 200,  type: "snare" },
  { id: "hihat", label: "HI-HAT", color: "#FFD60A", freq: 800,  type: "hihat" },
  { id: "clap",  label: "CLAP",   color: "#30D158", freq: 1200, type: "clap"  },
  { id: "perc",  label: "PERC",   color: "#64D2FF", freq: 400,  type: "perc"  },
  { id: "bass",  label: "808",    color: "#BF5AF2", freq: 40,   type: "bass"  },
];

const DEFAULT_PATTERN = {
  kick:  [1,0,0,0, 1,0,0,0, 1,0,0,0, 1,0,0,0],
  snare: [0,0,0,0, 1,0,0,0, 0,0,0,0, 1,0,0,0],
  hihat: [1,0,1,0, 1,0,1,0, 1,0,1,0, 1,0,1,1],
  clap:  [0,0,0,0, 0,0,0,0, 0,0,0,0, 0,0,0,0],
  perc:  [0,0,0,1, 0,0,0,0, 0,0,1,0, 0,0,0,0],
  bass:  [1,0,0,0, 0,0,1,0, 0,0,0,0, 1,0,0,0],
};

// ── Web Audio Drum Synthesis ──────────────────────────────────────────────────
// FIX: vol parameter is now passed in and applied via a masterGain node
function createDrum(ctx, type, freq, vol = 1) {
  const now = ctx.currentTime;

  // Master gain for volume control — FIX: was bypassed before
  const masterGain = ctx.createGain();
  masterGain.gain.value = vol;
  masterGain.connect(ctx.destination);

  if (type === "kick") {
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();
    osc.connect(gain);
    gain.connect(masterGain);
    osc.frequency.setValueAtTime(freq * 2.5, now);
    osc.frequency.exponentialRampToValueAtTime(freq * 0.5, now + 0.15);
    gain.gain.setValueAtTime(1.2, now);
    gain.gain.exponentialRampToValueAtTime(0.001, now + 0.4);
    osc.start(now); osc.stop(now + 0.4);

  } else if (type === "snare") {
    const gain = ctx.createGain();
    gain.connect(masterGain);
    const osc = ctx.createOscillator();
    const noise = ctx.createOscillator();
    const noiseGain = ctx.createGain();
    osc.connect(gain);
    noise.connect(noiseGain);
    noiseGain.connect(gain);
    osc.frequency.value = freq;
    noise.type = "sawtooth";
    noise.frequency.value = freq * 3;
    gain.gain.setValueAtTime(0.8, now);
    gain.gain.exponentialRampToValueAtTime(0.001, now + 0.2);
    noiseGain.gain.setValueAtTime(0.6, now);
    noiseGain.gain.exponentialRampToValueAtTime(0.001, now + 0.15);
    osc.start(now); osc.stop(now + 0.2);
    noise.start(now); noise.stop(now + 0.15);

  } else if (type === "hihat") {
    const bufferSize = ctx.sampleRate * 0.05;
    const buffer = ctx.createBuffer(1, bufferSize, ctx.sampleRate);
    const data = buffer.getChannelData(0);
    for (let i = 0; i < bufferSize; i++) data[i] = Math.random() * 2 - 1;
    const source = ctx.createBufferSource();
    const filter = ctx.createBiquadFilter();
    const gain = ctx.createGain();
    source.buffer = buffer;
    filter.type = "highpass";
    filter.frequency.value = 8000;
    source.connect(filter);
    filter.connect(gain);
    gain.connect(masterGain);
    gain.gain.setValueAtTime(0.6, now);
    gain.gain.exponentialRampToValueAtTime(0.001, now + 0.05);
    source.start(now);

  } else if (type === "clap") {
    for (let i = 0; i < 3; i++) {
      const bufferSize = ctx.sampleRate * 0.02;
      const buffer = ctx.createBuffer(1, bufferSize, ctx.sampleRate);
      const data = buffer.getChannelData(0);
      for (let j = 0; j < bufferSize; j++) data[j] = Math.random() * 2 - 1;
      const source = ctx.createBufferSource();
      source.buffer = buffer;
      const g = ctx.createGain();
      source.connect(g);
      g.connect(masterGain);
      g.gain.setValueAtTime(0.5, now + i * 0.01);
      g.gain.exponentialRampToValueAtTime(0.001, now + i * 0.01 + 0.03);
      source.start(now + i * 0.01);
    }

  } else if (type === "perc") {
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();
    osc.type = "triangle";
    osc.connect(gain);
    gain.connect(masterGain);
    osc.frequency.setValueAtTime(freq * 2, now);
    osc.frequency.exponentialRampToValueAtTime(freq * 0.3, now + 0.1);
    gain.gain.setValueAtTime(0.9, now);
    gain.gain.exponentialRampToValueAtTime(0.001, now + 0.12);
    osc.start(now); osc.stop(now + 0.12);

  } else if (type === "bass") {
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();
    osc.type = "sine";
    osc.connect(gain);
    gain.connect(masterGain);
    osc.frequency.setValueAtTime(freq * 3, now);
    osc.frequency.exponentialRampToValueAtTime(freq, now + 0.08);
    gain.gain.setValueAtTime(1.4, now);
    gain.gain.exponentialRampToValueAtTime(0.001, now + 0.6);
    osc.start(now); osc.stop(now + 0.6);
  }
}

// ── BPM Detector ─────────────────────────────────────────────────────────────
async function detectBPM(audioBuffer) {
  const data = audioBuffer.getChannelData(0);
  const sampleRate = audioBuffer.sampleRate;
  const windowSize = Math.floor(sampleRate * 0.01);
  const energies = [];
  for (let i = 0; i < data.length - windowSize; i += windowSize) {
    let e = 0;
    for (let j = 0; j < windowSize; j++) e += data[i + j] ** 2;
    energies.push(e / windowSize);
  }
  const peaks = [];
  const threshold = (energies.reduce((a, b) => a + b, 0) / energies.length) * 2;
  for (let i = 1; i < energies.length - 1; i++) {
    if (
      energies[i] > threshold &&
      energies[i] > energies[i - 1] &&
      energies[i] > energies[i + 1]
    ) {
      peaks.push(i);
    }
  }
  if (peaks.length < 2) return DEFAULT_BPM;
  const intervals = [];
  for (let i = 1; i < Math.min(peaks.length, 50); i++) {
    intervals.push(((peaks[i] - peaks[i - 1]) * windowSize) / sampleRate);
  }
  const avgInterval = intervals.reduce((a, b) => a + b, 0) / intervals.length;
  const bpm = Math.round(60 / avgInterval);
  return Math.max(60, Math.min(180, bpm));
}

// ─────────────────────────────────────────────────────────────────────────────

export default function BeatMaker() {
  const [bpm, setBpm] = useState(DEFAULT_BPM);
  const [pattern, setPattern] = useState(DEFAULT_PATTERN);
  const [playing, setPlaying] = useState(false);
  const [currentStep, setCurrentStep] = useState(-1);
  const [ytUrl, setYtUrl] = useState("");
  const [ytEmbedId, setYtEmbedId] = useState("");
  const [uploadName, setUploadName] = useState("");
  const [bpmDetecting, setBpmDetecting] = useState(false);
  const [detectedBpm, setDetectedBpm] = useState(null);
  const [activeTab, setActiveTab] = useState("beat");
  const [volume, setVolume] = useState(80);
  const [padFlash, setPadFlash] = useState({});

  const audioCtxRef = useRef(null);
  const intervalRef = useRef(null);
  const stepRef = useRef(0);
  const patternRef = useRef(pattern);
  const bpmRef = useRef(bpm);
  const volumeRef = useRef(volume);

  // FIX: tapBpm tap times stored in a ref so they survive re-renders
  const tapTimesRef = useRef([]);

  useEffect(() => { patternRef.current = pattern; }, [pattern]);
  useEffect(() => { bpmRef.current = bpm; }, [bpm]);
  useEffect(() => { volumeRef.current = volume; }, [volume]);

  const getCtx = useCallback(() => {
    if (!audioCtxRef.current || audioCtxRef.current.state === "closed") {
      audioCtxRef.current = new (window.AudioContext || window.webkitAudioContext)();
    }
    if (audioCtxRef.current.state === "suspended") audioCtxRef.current.resume();
    return audioCtxRef.current;
  }, []);

  const fireStep = useCallback((step) => {
    const ctx = getCtx();
    const pat = patternRef.current;
    // FIX: vol is now passed into createDrum so the volume slider actually works
    const vol = volumeRef.current / 100;
    const flashIds = [];

    PADS.forEach(({ id, freq, type }) => {
      if (pat[id][step]) {
        createDrum(ctx, type, freq, vol);
        flashIds.push(id);
      }
    });

    if (flashIds.length > 0) {
      setPadFlash((prev) => {
        const next = { ...prev };
        flashIds.forEach((id) => (next[id] = true));
        return next;
      });
      setTimeout(() => {
        setPadFlash((prev) => {
          const next = { ...prev };
          flashIds.forEach((id) => (next[id] = false));
          return next;
        });
      }, 80);
    }
  }, [getCtx]);

  const startStop = useCallback(() => {
    if (playing) {
      clearInterval(intervalRef.current);
      setPlaying(false);
      setCurrentStep(-1);
      stepRef.current = 0;
    } else {
      getCtx();
      setPlaying(true);
      stepRef.current = 0;
      const tick = () => {
        const step = stepRef.current % STEPS;
        setCurrentStep(step);
        fireStep(step);
        stepRef.current++;
      };
      tick();
      intervalRef.current = setInterval(tick, (60000 / bpmRef.current) / 4);
    }
  }, [playing, fireStep, getCtx]);

  // Restart interval when BPM changes while playing
  useEffect(() => {
    if (playing) {
      clearInterval(intervalRef.current);
      intervalRef.current = setInterval(() => {
        const step = stepRef.current % STEPS;
        setCurrentStep(step);
        fireStep(step);
        stepRef.current++;
      }, (60000 / bpm) / 4);
    }
  }, [bpm, playing, fireStep]);

  useEffect(() => () => clearInterval(intervalRef.current), []);

  const toggleStep = (padId, step) => {
    setPattern((prev) => ({
      ...prev,
      [padId]: prev[padId].map((v, i) => (i === step ? (v ? 0 : 1) : v)),
    }));
  };

  const clearPad = (padId) => {
    setPattern((prev) => ({ ...prev, [padId]: Array(STEPS).fill(0) }));
  };

  const extractYtId = (url) => {
    const match = url.match(/(?:youtu\.be\/|v=|embed\/)([A-Za-z0-9_-]{11})/);
    return match ? match[1] : null;
  };

  const loadYt = () => {
    const id = extractYtId(ytUrl);
    if (id) setYtEmbedId(id);
  };

  const handleAudioUpload = async (e) => {
    const file = e.target.files[0];
    if (!file) return;
    setUploadName(file.name);
    setBpmDetecting(true);
    try {
      const arrayBuffer = await file.arrayBuffer();
      const ctx = new AudioContext();
      const audioBuffer = await ctx.decodeAudioData(arrayBuffer);
      const detected = await detectBPM(audioBuffer);
      setDetectedBpm(detected);
      setBpm(detected);
      ctx.close();
    } catch {
      setDetectedBpm(null);
    }
    setBpmDetecting(false);
  };

  const randomize = () => {
    const newPat = {};
    PADS.forEach(({ id }) => {
      newPat[id] = Array(STEPS).fill(0).map(() => (Math.random() > 0.75 ? 1 : 0));
    });
    newPat.kick[0] = 1; newPat.kick[8] = 1;
    newPat.snare[4] = 1; newPat.snare[12] = 1;
    setPattern(newPat);
  };

  const resetPattern = () => setPattern(DEFAULT_PATTERN);

  // FIX: tapBpm now uses a ref for tap history — survives re-renders correctly
  const tapBpm = useCallback(() => {
    const now = Date.now();
    tapTimesRef.current.push(now);
    tapTimesRef.current = tapTimesRef.current
      .filter((t) => now - t < 3000)
      .slice(-8);
    if (tapTimesRef.current.length >= 2) {
      const diffs = tapTimesRef.current
        .slice(1)
        .map((t, i) => t - tapTimesRef.current[i]);
      const avg = diffs.reduce((a, b) => a + b, 0) / diffs.length;
      setBpm(Math.round(Math.max(60, Math.min(180, 60000 / avg))));
    }
  }, []);

  return (
    <div style={{
      minHeight: "100vh",
      background: "#0a0a0f",
      color: "#f0f0f0",
      fontFamily: "'Courier New', monospace",
      padding: "0",
      overflowX: "hidden",
    }}>
      {/* ── HEADER ── */}
      <div style={{
        background: "linear-gradient(135deg, #1a0a2e 0%, #0d1117 50%, #0a1a0d 100%)",
        padding: "20px 24px 0",
        borderBottom: "1px solid #222",
      }}>
        <div style={{ display: "flex", alignItems: "center", gap: 12, marginBottom: 16 }}>
          <div style={{
            width: 40, height: 40,
            background: "linear-gradient(135deg, #BF5AF2, #FF3B30)",
            borderRadius: 8,
            display: "flex", alignItems: "center", justifyContent: "center",
            fontSize: 20,
          }}>🎤</div>
          <div>
            <div style={{ fontSize: 20, fontWeight: 900, letterSpacing: 3, color: "#BF5AF2" }}>
              SAINT BEV
            </div>
            <div style={{ fontSize: 10, letterSpacing: 4, color: "#666", marginTop: -2 }}>
              SAMPLE BEAT STUDIO
            </div>
          </div>
        </div>

        {/* Tabs */}
        <div style={{ display: "flex", gap: 0 }}>
          {[
            { id: "beat",    label: "🥁 BEAT"     },
            { id: "youtube", label: "▶ YOUTUBE"   },
            { id: "upload",  label: "🎵 MY TRACK" },
          ].map((tab) => (
            <button key={tab.id} onClick={() => setActiveTab(tab.id)} style={{
              background: activeTab === tab.id ? "#BF5AF2" : "transparent",
              color: activeTab === tab.id ? "#fff" : "#666",
              border: "none",
              padding: "8px 18px",
              fontSize: 11,
              fontFamily: "'Courier New', monospace",
              fontWeight: 700,
              letterSpacing: 2,
              cursor: "pointer",
              borderRadius: "6px 6px 0 0",
              transition: "all 0.2s",
            }}>
              {tab.label}
            </button>
          ))}
        </div>
      </div>

      <div style={{ padding: "20px 16px" }}>

        {/* ── YOUTUBE TAB ── */}
        {activeTab === "youtube" && (
          <div>
            <div style={{ marginBottom: 16, color: "#888", fontSize: 12, lineHeight: 1.6 }}>
              Paste a YouTube link.<br />
              Play the song alongside your beat.<br />
              <span style={{ color: "#FF9500" }}>⚠ Can't rip audio — but you can hear it + rap over your beat.</span>
            </div>
            <div style={{ display: "flex", gap: 8, marginBottom: 16 }}>
              <input
                value={ytUrl}
                onChange={(e) => setYtUrl(e.target.value)}
                placeholder="https://youtube.com/watch?v=..."
                style={{
                  flex: 1, background: "#111", border: "1px solid #333",
                  color: "#f0f0f0", padding: "10px 12px", borderRadius: 6,
                  fontFamily: "'Courier New', monospace", fontSize: 12,
                  outline: "none",
                }}
              />
              <button onClick={loadYt} style={{
                background: "#FF3B30", border: "none", color: "#fff",
                padding: "10px 16px", borderRadius: 6, cursor: "pointer",
                fontWeight: 700, fontSize: 12, letterSpacing: 1,
                fontFamily: "'Courier New', monospace",
              }}>LOAD</button>
            </div>
            {ytEmbedId ? (
              <div style={{ borderRadius: 10, overflow: "hidden", border: "1px solid #333" }}>
                <iframe
                  width="100%" height="240"
                  src={`https://www.youtube.com/embed/${ytEmbedId}?rel=0`}
                  title="YouTube sample"
                  frameBorder="0"
                  allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
                  allowFullScreen
                  style={{ display: "block" }}
                />
              </div>
            ) : (
              <div style={{
                background: "#111", border: "1px dashed #333", borderRadius: 10,
                height: 160, display: "flex", alignItems: "center",
                justifyContent: "center", color: "#444", fontSize: 13,
              }}>
                ▶ Paste a YouTube link above
              </div>
            )}
            <div style={{ marginTop: 12, color: "#666", fontSize: 11, lineHeight: 1.8 }}>
              💡 TIP: Play the YouTube video, then tap the BEAT tab and hit PLAY to run the beat alongside the song.
            </div>
          </div>
        )}

        {/* ── UPLOAD TAB ── */}
        {activeTab === "upload" && (
          <div>
            <div style={{ marginBottom: 16, color: "#888", fontSize: 12, lineHeight: 1.6 }}>
              Upload one of your tracks.<br />
              It'll detect the BPM so the beat matches your flow.
            </div>
            <label style={{
              display: "block",
              background: "linear-gradient(135deg, #1a1a2e, #111)",
              border: "2px dashed #BF5AF2",
              borderRadius: 12,
              padding: "30px 20px",
              textAlign: "center",
              cursor: "pointer",
            }}>
              <input type="file" accept="audio/*" onChange={handleAudioUpload} style={{ display: "none" }} />
              <div style={{ fontSize: 32, marginBottom: 8 }}>🎵</div>
              <div style={{ color: "#BF5AF2", fontWeight: 700, letterSpacing: 2, fontSize: 13 }}>
                {uploadName || "TAP TO UPLOAD YOUR TRACK"}
              </div>
              <div style={{ color: "#555", fontSize: 11, marginTop: 4 }}>
                MP3, WAV, M4A supported
              </div>
            </label>

            {bpmDetecting && (
              <div style={{
                marginTop: 16, background: "#111", borderRadius: 8, padding: 16,
                textAlign: "center", color: "#BF5AF2", fontSize: 13, letterSpacing: 2,
              }}>
                ⏳ ANALYZING YOUR FLOW...
              </div>
            )}

            {detectedBpm && !bpmDetecting && (
              <div style={{
                marginTop: 16,
                background: "linear-gradient(135deg, #1a0a2e, #0a1a0d)",
                border: "1px solid #BF5AF2",
                borderRadius: 10,
                padding: 16,
              }}>
                <div style={{ color: "#888", fontSize: 11, letterSpacing: 2, marginBottom: 4 }}>
                  DETECTED TEMPO
                </div>
                <div style={{ fontSize: 40, fontWeight: 900, color: "#BF5AF2", letterSpacing: 2 }}>
                  {detectedBpm} <span style={{ fontSize: 16, color: "#666" }}>BPM</span>
                </div>
                <div style={{ color: "#30D158", fontSize: 12, marginTop: 4 }}>
                  ✓ Beat automatically set to match
                </div>
                <div style={{ color: "#666", fontSize: 11, marginTop: 8 }}>
                  Go to BEAT tab and hit PLAY — the groove is locked to your flow.
                </div>
              </div>
            )}
          </div>
        )}

        {/* ── BEAT TAB ── */}
        {activeTab === "beat" && (
          <div>
            {/* Transport + BPM */}
            <div style={{
              display: "flex", alignItems: "center", gap: 12,
              marginBottom: 20,
              background: "#111",
              borderRadius: 10,
              padding: "12px 16px",
              border: "1px solid #222",
            }}>
              <button onClick={startStop} style={{
                width: 52, height: 52,
                background: playing
                  ? "linear-gradient(135deg, #FF3B30, #FF9500)"
                  : "linear-gradient(135deg, #30D158, #64D2FF)",
                border: "none", borderRadius: 10, fontSize: 22,
                cursor: "pointer", flexShrink: 0,
                display: "flex", alignItems: "center", justifyContent: "center",
                boxShadow: playing ? "0 0 20px #FF3B3066" : "0 0 20px #30D15866",
                transition: "all 0.2s",
              }}>
                {playing ? "⏹" : "▶"}
              </button>

              <div style={{ flex: 1 }}>
                <div style={{
                  display: "flex", alignItems: "center",
                  justifyContent: "space-between", marginBottom: 4,
                }}>
                  <span style={{ fontSize: 10, color: "#666", letterSpacing: 2 }}>BPM</span>
                  <span style={{ fontSize: 18, fontWeight: 900, color: "#FFD60A" }}>{bpm}</span>
                </div>
                <input
                  type="range" min="60" max="180" value={bpm}
                  onChange={(e) => setBpm(Number(e.target.value))}
                  style={{ width: "100%", accentColor: "#FFD60A" }}
                />
              </div>

              <button onClick={tapBpm} style={{
                background: "#222", border: "1px solid #444",
                color: "#FFD60A", padding: "8px 12px",
                borderRadius: 8, cursor: "pointer", fontSize: 10,
                fontFamily: "'Courier New', monospace", fontWeight: 700,
                letterSpacing: 1, flexShrink: 0,
              }}>TAP</button>
            </div>

            {/* Volume — FIX: now actually controls drum volume */}
            <div style={{
              display: "flex", alignItems: "center", gap: 10,
              marginBottom: 16, padding: "0 4px",
            }}>
              <span style={{ fontSize: 10, color: "#666", letterSpacing: 2, flexShrink: 0 }}>VOL</span>
              <input
                type="range" min="0" max="100" value={volume}
                onChange={(e) => setVolume(Number(e.target.value))}
                style={{ flex: 1, accentColor: "#BF5AF2" }}
              />
              <span style={{ fontSize: 12, color: "#BF5AF2", width: 30, textAlign: "right" }}>{volume}</span>
            </div>

            {/* Step Sequencer */}
            <div style={{ marginBottom: 16 }}>
              {/* Step numbers */}
              <div style={{
                display: "grid",
                gridTemplateColumns: "52px repeat(16, 1fr)",
                gap: 3,
                marginBottom: 4,
              }}>
                <div />
                {Array(STEPS).fill(0).map((_, i) => (
                  <div key={i} style={{
                    textAlign: "center", fontSize: 8,
                    color: i % 4 === 0 ? "#FFD60A" : "#444",
                    fontWeight: i % 4 === 0 ? 900 : 400,
                  }}>{i + 1}</div>
                ))}
              </div>

              {PADS.map(({ id, label, color }) => (
                <div key={id} style={{
                  display: "grid",
                  gridTemplateColumns: "52px repeat(16, 1fr)",
                  gap: 3,
                  marginBottom: 4,
                }}>
                  {/* Pad label — click to clear */}
                  <button
                    onClick={() => clearPad(id)}
                    title="Click to clear"
                    style={{
                      background: padFlash[id] ? color : `${color}22`,
                      border: `1px solid ${color}66`,
                      color: padFlash[id] ? "#fff" : color,
                      borderRadius: 4,
                      fontSize: 8, fontWeight: 900,
                      letterSpacing: 0.5,
                      cursor: "pointer",
                      fontFamily: "'Courier New', monospace",
                      transition: "all 0.08s",
                      boxShadow: padFlash[id] ? `0 0 12px ${color}` : "none",
                      padding: 0,
                    }}>
                    {label}
                  </button>

                  {/* Steps */}
                  {pattern[id].map((active, step) => {
                    const isCurrentStep = step === currentStep;
                    const isOn = active === 1;
                    const isBeat = step % 4 === 0;
                    return (
                      <button
                        key={step}
                        onClick={() => toggleStep(id, step)}
                        style={{
                          height: 32,
                          background: isOn
                            ? isCurrentStep ? "#fff" : color
                            : isCurrentStep ? "#333" : isBeat ? "#1a1a1a" : "#141414",
                          border: isOn
                            ? `1px solid ${color}`
                            : `1px solid ${isBeat ? "#2a2a2a" : "#1e1e1e"}`,
                          borderRadius: 3,
                          cursor: "pointer",
                          transition: "all 0.06s",
                          boxShadow: isOn && isCurrentStep ? `0 0 10px ${color}` : "none",
                          padding: 0,
                        }}
                      />
                    );
                  })}
                </div>
              ))}
            </div>

            {/* Action buttons */}
            <div style={{ display: "flex", gap: 8 }}>
              <button onClick={randomize} style={{
                flex: 1,
                background: "#1a1a2e", border: "1px solid #333",
                color: "#64D2FF", padding: "10px 0",
                borderRadius: 8, cursor: "pointer",
                fontSize: 11, fontWeight: 700, letterSpacing: 2,
                fontFamily: "'Courier New', monospace",
              }}>🎲 RANDOMIZE</button>
              <button onClick={resetPattern} style={{
                flex: 1,
                background: "#1a1a2e", border: "1px solid #333",
                color: "#FF9500", padding: "10px 0",
                borderRadius: 8, cursor: "pointer",
                fontSize: 11, fontWeight: 700, letterSpacing: 2,
                fontFamily: "'Courier New', monospace",
              }}>↺ RESET</button>
            </div>

            {playing && (
              <div style={{
                marginTop: 12,
                background: "#0d1a0d",
                border: "1px solid #30D158",
                borderRadius: 8,
                padding: "8px 12px",
                fontSize: 11,
                color: "#30D158",
                letterSpacing: 1,
                textAlign: "center",
              }}>
                🎤 BEAT RUNNING — START RAPPING
              </div>
            )}
          </div>
        )}
      </div>
    </div>
  );
}
