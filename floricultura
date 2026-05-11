import { useState, useEffect } from "react";

const STATUS = {
  pendente: { label: "Pendente", color: "#b45309", bg: "#fef3c7" },
  preparo: { label: "Em preparo", color: "#0f766e", bg: "#ccfbf1" },
  pronto: { label: "Pronto", color: "#1d4ed8", bg: "#dbeafe" },
  entregue: { label: "Entregue", color: "#15803d", bg: "#dcfce7" },
};

const EMPTY_FORM = {
  cliente: "", telefone: "", itens: "", valor: "", entrega: "", obs: "", status: "pendente",
};

function Badge({ status }) {
  const s = STATUS[status];
  return (
    <span style={{
      background: s.bg, color: s.color, borderRadius: 20,
      padding: "3px 10px", fontSize: 12, fontWeight: 600, whiteSpace: "nowrap",
    }}>{s.label}</span>
  );
}

function formatBRL(val) {
  const n = parseFloat(val);
  if (isNaN(n)) return "";
  return n.toLocaleString("pt-BR", { style: "currency", currency: "BRL" });
}

function formatDate(val) {
  if (!val) return "—";
  const [y, m, d] = val.split("-");
  return `${d}/${m}/${y}`;
}

export default function Floricultura() {
  const [orders, setOrders] = useState([]);
  const [view, setView] = useState("lista"); // lista | novo | detalhe
  const [form, setForm] = useState(EMPTY_FORM);
  const [editId, setEditId] = useState(null);
  const [filtro, setFiltro] = useState("todos");
  const [selectedId, setSelectedId] = useState(null);
  const [loading, setLoading] = useState(true);
  const [saving, setSaving] = useState(false);
  const [toast, setToast] = useState(null);

  useEffect(() => {
    (async () => {
      try {
        const r = await window.storage.get("flor_orders");
        if (r) setOrders(JSON.parse(r.value));
      } catch (_) {}
      setLoading(false);
    })();
  }, []);

  async function saveOrders(list) {
    setSaving(true);
    try { await window.storage.set("flor_orders", JSON.stringify(list)); } catch (_) {}
    setSaving(false);
  }

  function showToast(msg) {
    setToast(msg);
    setTimeout(() => setToast(null), 2500);
  }

  function handleSubmit() {
    if (!form.cliente.trim() || !form.itens.trim()) return;
    let updated;
    if (editId) {
      updated = orders.map(o => o.id === editId ? { ...o, ...form } : o);
      showToast("Pedido atualizado!");
    } else {
      const newOrder = { ...form, id: Date.now().toString(), criadoEm: new Date().toISOString() };
      updated = [newOrder, ...orders];
      showToast("Pedido criado!");
    }
    setOrders(updated);
    saveOrders(updated);
    setForm(EMPTY_FORM);
    setEditId(null);
    setView("lista");
  }

  function handleDelete(id) {
    const updated = orders.filter(o => o.id !== id);
    setOrders(updated);
    saveOrders(updated);
    setView("lista");
    setSelectedId(null);
    showToast("Pedido removido.");
  }

  function handleStatusChange(id, status) {
    const updated = orders.map(o => o.id === id ? { ...o, status } : o);
    setOrders(updated);
    saveOrders(updated);
    showToast("Status atualizado!");
  }

  function startEdit(order) {
    setForm({ cliente: order.cliente, telefone: order.telefone, itens: order.itens, valor: order.valor, entrega: order.entrega, obs: order.obs, status: order.status });
    setEditId(order.id);
    setView("novo");
  }

  const filtrados = orders.filter(o => filtro === "todos" || o.status === filtro);
  const counts = Object.fromEntries(Object.keys(STATUS).map(k => [k, orders.filter(o => o.status === k).length]));
  const selected = orders.find(o => o.id === selectedId);

  if (loading) return (
    <div style={{ display: "flex", alignItems: "center", justifyContent: "center", height: 200, color: "#6b7280", fontFamily: "Georgia, serif" }}>
      Carregando pedidos...
    </div>
  );

  return (
    <div style={{ fontFamily: "'Georgia', serif", maxWidth: 700, margin: "0 auto", padding: "0 0 2rem" }}>

      {/* Toast */}
      {toast && (
        <div style={{
          position: "fixed", bottom: 24, left: "50%", transform: "translateX(-50%)",
          background: "#1a1a1a", color: "#fff", padding: "10px 20px", borderRadius: 30,
          fontSize: 14, zIndex: 999, whiteSpace: "nowrap", boxShadow: "0 4px 16px rgba(0,0,0,0.2)"
        }}>{toast}</div>
      )}

      {/* Header */}
      <div style={{ padding: "1.5rem 0 1rem", borderBottom: "1.5px solid #e5e0d8", marginBottom: "1.25rem", display: "flex", alignItems: "center", justifyContent: "space-between" }}>
        <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
          <span style={{ fontSize: 28 }}>🌸</span>
          <div>
            <h1 style={{ margin: 0, fontSize: 22, fontWeight: 700, color: "#2d1f0e", letterSpacing: "-0.5px" }}>Floricultura</h1>
            <p style={{ margin: 0, fontSize: 13, color: "#9c7a5a" }}>Gestão de pedidos</p>
          </div>
        </div>
        {view !== "novo" ? (
          <button onClick={() => { setForm(EMPTY_FORM); setEditId(null); setView("novo"); }} style={{
            background: "#2d6a4f", color: "#fff", border: "none", borderRadius: 24,
            padding: "8px 18px", fontSize: 14, cursor: "pointer", fontFamily: "inherit", fontWeight: 600
          }}>+ Novo pedido</button>
        ) : (
          <button onClick={() => { setView("lista"); setForm(EMPTY_FORM); setEditId(null); }} style={{
            background: "none", border: "1.5px solid #c5b49a", color: "#6b4e31",
            borderRadius: 24, padding: "7px 16px", fontSize: 13, cursor: "pointer", fontFamily: "inherit"
          }}>← Cancelar</button>
        )}
      </div>

      {/* FORM */}
      {view === "novo" && (
        <div style={{ background: "#faf7f2", border: "1px solid #e5ddd0", borderRadius: 14, padding: "1.5rem" }}>
          <h2 style={{ margin: "0 0 1.2rem", fontSize: 17, color: "#2d1f0e", fontWeight: 700 }}>
            {editId ? "Editar pedido" : "Novo pedido"}
          </h2>
          <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 14 }}>
            {[
              { label: "Nome do cliente *", key: "cliente", full: true },
              { label: "Telefone", key: "telefone" },
              { label: "Data de entrega", key: "entrega", type: "date" },
              { label: "Valor (R$)", key: "valor", type: "number" },
            ].map(({ label, key, full, type }) => (
              <div key={key} style={{ gridColumn: full ? "1 / -1" : undefined }}>
                <label style={{ display: "block", fontSize: 12, color: "#6b4e31", marginBottom: 5, fontWeight: 600 }}>{label}</label>
                <input
                  type={type || "text"}
                  value={form[key]}
                  onChange={e => setForm(f => ({ ...f, [key]: e.target.value }))}
                  style={{
                    width: "100%", boxSizing: "border-box", padding: "9px 12px",
                    border: "1px solid #d6c9b5", borderRadius: 8, fontSize: 14,
                    background: "#fff", fontFamily: "inherit", outline: "none", color: "#2d1f0e"
                  }}
                />
              </div>
            ))}
            <div style={{ gridColumn: "1 / -1" }}>
              <label style={{ display: "block", fontSize: 12, color: "#6b4e31", marginBottom: 5, fontWeight: 600 }}>Itens do pedido *</label>
              <textarea
                value={form.itens} rows={3}
                onChange={e => setForm(f => ({ ...f, itens: e.target.value }))}
                placeholder="Ex: 1 buquê de rosas vermelhas, 2 orquídeas brancas..."
                style={{
                  width: "100%", boxSizing: "border-box", padding: "9px 12px",
                  border: "1px solid #d6c9b5", borderRadius: 8, fontSize: 14,
                  background: "#fff", fontFamily: "inherit", resize: "vertical", color: "#2d1f0e"
                }}
              />
            </div>
            <div style={{ gridColumn: "1 / -1" }}>
              <label style={{ display: "block", fontSize: 12, color: "#6b4e31", marginBottom: 5, fontWeight: 600 }}>Observações</label>
              <textarea
                value={form.obs} rows={2}
                onChange={e => setForm(f => ({ ...f, obs: e.target.value }))}
                placeholder="Ex: entregar na portaria, cliente alérgico a cravos..."
                style={{
                  width: "100%", boxSizing: "border-box", padding: "9px 12px",
                  border: "1px solid #d6c9b5", borderRadius: 8, fontSize: 14,
                  background: "#fff", fontFamily: "inherit", resize: "vertical", color: "#2d1f0e"
                }}
              />
            </div>
            <div>
              <label style={{ display: "block", fontSize: 12, color: "#6b4e31", marginBottom: 5, fontWeight: 600 }}>Status</label>
              <select
                value={form.status}
                onChange={e => setForm(f => ({ ...f, status: e.target.value }))}
                style={{
                  width: "100%", padding: "9px 12px", border: "1px solid #d6c9b5",
                  borderRadius: 8, fontSize: 14, background: "#fff", fontFamily: "inherit", color: "#2d1f0e"
                }}
              >
                {Object.entries(STATUS).map(([k, v]) => <option key={k} value={k}>{v.label}</option>)}
              </select>
            </div>
          </div>
          <button
            onClick={handleSubmit}
            disabled={!form.cliente.trim() || !form.itens.trim() || saving}
            style={{
              marginTop: "1.25rem", background: "#2d6a4f", color: "#fff", border: "none",
              borderRadius: 24, padding: "10px 28px", fontSize: 15, cursor: "pointer",
              fontFamily: "inherit", fontWeight: 700, opacity: (!form.cliente.trim() || !form.itens.trim()) ? 0.5 : 1
            }}
          >
            {editId ? "Salvar alterações" : "Criar pedido"}
          </button>
        </div>
      )}

      {/* LISTA */}
      {view === "lista" && (
        <>
          {/* Stats */}
          <div style={{ display: "grid", gridTemplateColumns: "repeat(4, 1fr)", gap: 10, marginBottom: "1.25rem" }}>
            {Object.entries(STATUS).map(([k, v]) => (
              <div key={k} style={{ background: v.bg, borderRadius: 10, padding: "10px 12px", textAlign: "center" }}>
                <div style={{ fontSize: 22, fontWeight: 700, color: v.color }}>{counts[k]}</div>
                <div style={{ fontSize: 11, color: v.color, fontWeight: 600 }}>{v.label}</div>
              </div>
            ))}
          </div>

          {/* Filtros */}
          <div style={{ display: "flex", gap: 8, marginBottom: "1rem", flexWrap: "wrap" }}>
            {[["todos", "Todos"], ...Object.entries(STATUS).map(([k, v]) => [k, v.label])].map(([k, label]) => (
              <button key={k} onClick={() => setFiltro(k)} style={{
                padding: "5px 14px", borderRadius: 20, border: "1.5px solid",
                borderColor: filtro === k ? "#2d6a4f" : "#d6c9b5",
                background: filtro === k ? "#2d6a4f" : "transparent",
                color: filtro === k ? "#fff" : "#6b4e31",
                fontSize: 13, cursor: "pointer", fontFamily: "inherit", fontWeight: 600
              }}>{label}</button>
            ))}
          </div>

          {/* Lista */}
          {filtrados.length === 0 ? (
            <div style={{ textAlign: "center", padding: "3rem 0", color: "#9c7a5a", fontSize: 15 }}>
              🌿 Nenhum pedido encontrado.
            </div>
          ) : (
            <div style={{ display: "flex", flexDirection: "column", gap: 10 }}>
              {filtrados.map(order => (
                <div key={order.id}
                  onClick={() => { setSelectedId(order.id); setView("detalhe"); }}
                  style={{
                    background: "#faf7f2", border: "1px solid #e5ddd0", borderRadius: 12,
                    padding: "12px 16px", cursor: "pointer", transition: "border-color .15s",
                    display: "grid", gridTemplateColumns: "1fr auto", gap: "4px 12px", alignItems: "start"
                  }}
                >
                  <div>
                    <div style={{ fontWeight: 700, color: "#2d1f0e", fontSize: 15 }}>{order.cliente}</div>
                    <div style={{ fontSize: 13, color: "#6b4e31", marginTop: 2, whiteSpace: "nowrap", overflow: "hidden", textOverflow: "ellipsis", maxWidth: 380 }}>{order.itens}</div>
                    <div style={{ fontSize: 12, color: "#9c7a5a", marginTop: 4 }}>
                      {order.entrega ? `📅 ${formatDate(order.entrega)}` : "Sem data"}
                      {order.telefone ? ` · 📞 ${order.telefone}` : ""}
                    </div>
                  </div>
                  <div style={{ textAlign: "right", display: "flex", flexDirection: "column", gap: 6, alignItems: "flex-end" }}>
                    <Badge status={order.status} />
                    {order.valor ? <div style={{ fontSize: 14, fontWeight: 700, color: "#2d6a4f" }}>{formatBRL(order.valor)}</div> : null}
                  </div>
                </div>
              ))}
            </div>
          )}
        </>
      )}

      {/* DETALHE */}
      {view === "detalhe" && selected && (
        <div>
          <button onClick={() => setView("lista")} style={{
            background: "none", border: "none", cursor: "pointer", fontSize: 14,
            color: "#6b4e31", fontFamily: "inherit", padding: "0 0 1rem", display: "block"
          }}>← Voltar para lista</button>

          <div style={{ background: "#faf7f2", border: "1px solid #e5ddd0", borderRadius: 14, padding: "1.5rem" }}>
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginBottom: "1rem" }}>
              <div>
                <h2 style={{ margin: "0 0 4px", fontSize: 20, color: "#2d1f0e" }}>{selected.cliente}</h2>
                {selected.telefone && <div style={{ fontSize: 14, color: "#6b4e31" }}>📞 {selected.telefone}</div>}
              </div>
              <Badge status={selected.status} />
            </div>

            <div style={{ borderTop: "1px solid #e5ddd0", paddingTop: "1rem", display: "flex", flexDirection: "column", gap: 10 }}>
              <div>
                <div style={{ fontSize: 11, color: "#9c7a5a", fontWeight: 600, marginBottom: 3 }}>ITENS DO PEDIDO</div>
                <div style={{ fontSize: 15, color: "#2d1f0e" }}>{selected.itens}</div>
              </div>
              {selected.entrega && (
                <div>
                  <div style={{ fontSize: 11, color: "#9c7a5a", fontWeight: 600, marginBottom: 3 }}>DATA DE ENTREGA</div>
                  <div style={{ fontSize: 15, color: "#2d1f0e" }}>{formatDate(selected.entrega)}</div>
                </div>
              )}
              {selected.valor && (
                <div>
                  <div style={{ fontSize: 11, color: "#9c7a5a", fontWeight: 600, marginBottom: 3 }}>VALOR</div>
                  <div style={{ fontSize: 16, fontWeight: 700, color: "#2d6a4f" }}>{formatBRL(selected.valor)}</div>
                </div>
              )}
              {selected.obs && (
                <div>
                  <div style={{ fontSize: 11, color: "#9c7a5a", fontWeight: 600, marginBottom: 3 }}>OBSERVAÇÕES</div>
                  <div style={{ fontSize: 14, color: "#6b4e31" }}>{selected.obs}</div>
                </div>
              )}
            </div>

            {/* Mudar status */}
            <div style={{ marginTop: "1.25rem", borderTop: "1px solid #e5ddd0", paddingTop: "1rem" }}>
              <div style={{ fontSize: 11, color: "#9c7a5a", fontWeight: 600, marginBottom: 8 }}>ATUALIZAR STATUS</div>
              <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
                {Object.entries(STATUS).map(([k, v]) => (
                  <button key={k} onClick={() => handleStatusChange(selected.id, k)} style={{
                    padding: "6px 14px", borderRadius: 20,
                    border: selected.status === k ? "2px solid " + v.color : "1.5px solid #d6c9b5",
                    background: selected.status === k ? v.bg : "transparent",
                    color: selected.status === k ? v.color : "#6b4e31",
                    fontSize: 13, cursor: "pointer", fontFamily: "inherit", fontWeight: 600
                  }}>{v.label}</button>
                ))}
              </div>
            </div>

            {/* Ações */}
            <div style={{ display: "flex", gap: 10, marginTop: "1.25rem" }}>
              <button onClick={() => startEdit(selected)} style={{
                flex: 1, padding: "10px", background: "#2d6a4f", color: "#fff",
                border: "none", borderRadius: 10, fontSize: 14, cursor: "pointer", fontFamily: "inherit", fontWeight: 600
              }}>✏️ Editar pedido</button>
              <button onClick={() => { if (confirm("Remover este pedido?")) handleDelete(selected.id); }} style={{
                padding: "10px 16px", background: "none", color: "#b91c1c",
                border: "1.5px solid #fca5a5", borderRadius: 10, fontSize: 14, cursor: "pointer", fontFamily: "inherit"
              }}>🗑️ Remover</button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}
