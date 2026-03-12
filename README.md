# Smart_Bridge_project
import { useState, useEffect, useRef } from "react";

const products = [
  { id: 1, name: "Arc Lamp Pro", category: "Home", price: 189, originalPrice: 240, rating: 4.8, reviews: 312, discount: 21, image: "🪔", tag: "Bestseller", description: "Sleek arc floor lamp with adjustable brightness and modern design." },
  { id: 2, name: "CloudStep Sneakers", category: "Fashion", price: 129, originalPrice: 160, rating: 4.7, reviews: 521, discount: 19, image: "👟", tag: "Trending", description: "Ultra-light foam sole sneakers with breathable mesh upper." },
  { id: 3, name: "Aura Wireless Buds", category: "Electronics", price: 89, originalPrice: 120, rating: 4.9, reviews: 874, discount: 26, image: "🎧", tag: "Top Rated", description: "36-hour playtime, active noise cancellation, premium sound." },
  { id: 4, name: "Linen Dream Throw", category: "Home", price: 54, originalPrice: 75, rating: 4.6, reviews: 203, discount: 28, image: "🛋️", tag: "New", description: "100% organic linen throw, naturally dyed, ultra-soft texture." },
  { id: 5, name: "Ember Smartwatch", category: "Electronics", price: 249, originalPrice: 299, rating: 4.8, reviews: 643, discount: 17, image: "⌚", tag: "Featured", description: "Health tracking, GPS, 7-day battery. Lightweight aluminum case." },
  { id: 6, name: "Noir Perfume Set", category: "Beauty", price: 79, originalPrice: 110, rating: 4.7, reviews: 287, discount: 28, image: "🌸", tag: "Sale", description: "Curated trio of long-lasting luxury fragrances in travel sizes." },
  { id: 7, name: "Chef's Knife Set", category: "Kitchen", price: 119, originalPrice: 150, rating: 4.9, reviews: 431, discount: 21, image: "🔪", tag: "Bestseller", description: "German steel, full-tang 5-piece professional knife collection." },
  { id: 8, name: "Peak Backpack 30L", category: "Fashion", price: 95, originalPrice: 130, rating: 4.8, reviews: 352, discount: 27, image: "🎒", tag: "Trending", description: "Waterproof, ergonomic, 30L with laptop sleeve and USB port." },
];

const categories = ["All", "Electronics", "Fashion", "Home", "Beauty", "Kitchen"];

const reviews = [
  { user: "Maya R.", rating: 5, text: "ShopEZ completely changed how I shop online. The UI is stunning and checkout is a breeze.", product: "Aura Wireless Buds", avatar: "M" },
  { user: "James K.", rating: 5, text: "Got my order in 2 days, product quality is amazing. Will definitely shop here again!", product: "CloudStep Sneakers", avatar: "J" },
  { user: "Priya S.", rating: 5, text: "The seller dashboard is incredibly intuitive. My sales have grown 40% since switching.", product: "Seller", avatar: "P" },
];

const sellerStats = [
  { label: "Total Orders", value: "2,847", icon: "📦", change: "+12%" },
  { label: "Revenue", value: "$48,320", icon: "💰", change: "+18%" },
  { label: "Customers", value: "1,204", icon: "👥", change: "+8%" },
  { label: "Rating", value: "4.9★", icon: "⭐", change: "+0.2" },
];

export default function ShopEZ() {
  const [page, setPage] = useState("home");
  const [cart, setCart] = useState([]);
  const [wishlist, setWishlist] = useState([]);
  const [search, setSearch] = useState("");
  const [category, setCategory] = useState("All");
  const [selectedProduct, setSelectedProduct] = useState(null);
  const [cartOpen, setCartOpen] = useState(false);
  const [checkoutStep, setCheckoutStep] = useState(1);
  const [orderPlaced, setOrderPlaced] = useState(false);
  const [toast, setToast] = useState(null);
  const [menuOpen, setMenuOpen] = useState(false);
  const heroRef = useRef(null);

  const showToast = (msg, type = "success") => {
    setToast({ msg, type });
    setTimeout(() => setToast(null), 2800);
  };

  const addToCart = (product) => {
    setCart(prev => {
      const existing = prev.find(i => i.id === product.id);
      if (existing) return prev.map(i => i.id === product.id ? { ...i, qty: i.qty + 1 } : i);
      return [...prev, { ...product, qty: 1 }];
    });
    showToast(`${product.name} added to cart`);
  };

  const toggleWishlist = (product) => {
    setWishlist(prev => {
      const exists = prev.find(i => i.id === product.id);
      if (exists) { showToast("Removed from wishlist", "info"); return prev.filter(i => i.id !== product.id); }
      showToast("Added to wishlist ❤️"); return [...prev, product];
    });
  };

  const removeFromCart = (id) => setCart(prev => prev.filter(i => i.id !== id));
  const updateQty = (id, qty) => {
    if (qty < 1) return removeFromCart(id);
    setCart(prev => prev.map(i => i.id === id ? { ...i, qty } : i));
  };

  const cartTotal = cart.reduce((s, i) => s + i.price * i.qty, 0);
  const cartCount = cart.reduce((s, i) => s + i.qty, 0);

  const filtered = products.filter(p =>
    (category === "All" || p.category === category) &&
    (p.name.toLowerCase().includes(search.toLowerCase()) || p.category.toLowerCase().includes(search.toLowerCase()))
  );

  const placeOrder = () => {
    setOrderPlaced(true);
    setCart([]);
    setCartOpen(false);
    setCheckoutStep(1);
  };

  useEffect(() => {
    const el = heroRef.current;
    if (!el) return;
    const move = (e) => {
      const { left, top, width, height } = el.getBoundingClientRect();
      const x = ((e.clientX - left) / width - 0.5) * 18;
      const y = ((e.clientY - top) / height - 0.5) * 18;
      el.style.setProperty("--rx", `${y}deg`);
      el.style.setProperty("--ry", `${-x}deg`);
    };
    el.addEventListener("mousemove", move);
    return () => el.removeEventListener("mousemove", move);
  }, [page]);

  return (
    <div style={{ fontFamily: "'DM Sans', 'Segoe UI', sans-serif", background: "#0a0a0f", minHeight: "100vh", color: "#f0ece8", overflowX: "hidden" }}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=DM+Sans:wght@300;400;500;600;700&family=Playfair+Display:wght@700;800&display=swap');
        * { box-sizing: border-box; margin: 0; padding: 0; }
        ::-webkit-scrollbar { width: 6px; } ::-webkit-scrollbar-track { background: #0a0a0f; } ::-webkit-scrollbar-thumb { background: #ff5a1f; border-radius: 3px; }
        .nav-link { cursor: pointer; color: #a09d98; transition: color .2s; font-size: 15px; font-weight: 500; }
        .nav-link:hover, .nav-link.active { color: #ff5a1f; }
        .card { background: #14141c; border: 1px solid #22222e; border-radius: 20px; transition: transform .3s, box-shadow .3s, border-color .3s; overflow: hidden; }
        .card:hover { transform: translateY(-6px); box-shadow: 0 20px 60px rgba(255,90,31,.15); border-color: #ff5a1f44; }
        .btn-primary { background: linear-gradient(135deg, #ff5a1f, #ff8c42); color: #fff; border: none; border-radius: 12px; padding: 12px 24px; font-size: 15px; font-weight: 600; cursor: pointer; transition: opacity .2s, transform .15s; letter-spacing: .3px; }
        .btn-primary:hover { opacity: .9; transform: scale(1.02); }
        .btn-ghost { background: transparent; border: 1.5px solid #33333f; color: #f0ece8; border-radius: 12px; padding: 11px 22px; font-size: 15px; font-weight: 500; cursor: pointer; transition: all .2s; }
        .btn-ghost:hover { border-color: #ff5a1f; color: #ff5a1f; }
        .tag { display: inline-block; padding: 3px 10px; border-radius: 30px; font-size: 11px; font-weight: 700; letter-spacing: .5px; text-transform: uppercase; }
        .tag-orange { background: #ff5a1f22; color: #ff5a1f; border: 1px solid #ff5a1f44; }
        .tag-green { background: #1af59522; color: #1af595; border: 1px solid #1af59544; }
        .tag-blue { background: #1ab6ff22; color: #1ab6ff; border: 1px solid #1ab6ff44; }
        .stars { color: #f5c842; font-size: 13px; letter-spacing: 1px; }
        input, select, textarea { background: #1c1c26; border: 1.5px solid #2e2e3e; border-radius: 12px; padding: 12px 16px; color: #f0ece8; font-size: 14px; outline: none; transition: border-color .2s; font-family: inherit; }
        input:focus, select:focus, textarea:focus { border-color: #ff5a1f; }
        .overlay { position: fixed; inset: 0; background: rgba(0,0,0,.7); backdrop-filter: blur(4px); z-index: 100; display: flex; align-items: center; justify-content: center; }
        .modal { background: #14141c; border: 1px solid #2e2e3e; border-radius: 24px; padding: 32px; max-width: 520px; width: 95%; max-height: 90vh; overflow-y: auto; position: relative; }
        .drawer { position: fixed; top: 0; right: 0; bottom: 0; width: min(420px, 100vw); background: #14141c; border-left: 1px solid #22222e; z-index: 200; padding: 28px 24px; overflow-y: auto; transform: translateX(0); }
        .hero-card { transform: perspective(1000px) rotateX(var(--rx,0)) rotateY(var(--ry,0)); transition: transform .1s ease-out; }
        .badge { position: absolute; top: -8px; right: -8px; background: #ff5a1f; color: white; border-radius: 50%; width: 20px; height: 20px; font-size: 11px; font-weight: 700; display: flex; align-items: center; justify-content: center; }
        .shimmer { animation: shimmer 2s infinite; }
        @keyframes shimmer { 0%,100% { opacity:1 } 50% { opacity:.7 } }
        .fade-in { animation: fadeIn .4s ease forwards; }
        @keyframes fadeIn { from { opacity:0; transform:translateY(12px) } to { opacity:1; transform:translateY(0) } }
        .progress-step { display: flex; align-items: center; gap: 8px; font-size: 13px; font-weight: 600; color: #555; }
        .progress-step.active { color: #ff5a1f; }
        .progress-step.done { color: #1af595; }
        .step-circle { width: 28px; height: 28px; border-radius: 50%; border: 2px solid currentColor; display: flex; align-items: center; justify-content: center; font-size: 12px; flex-shrink: 0; }
        .step-line { flex: 1; height: 2px; background: currentColor; opacity: .3; }
        .stat-card { background: linear-gradient(135deg, #1a1a26, #14141c); border: 1px solid #2e2e3e; border-radius: 16px; padding: 20px; transition: transform .2s; }
        .stat-card:hover { transform: scale(1.02); }
        .toast { position: fixed; bottom: 28px; left: 50%; transform: translateX(-50%); background: #1c1c28; border: 1px solid #ff5a1f55; border-radius: 14px; padding: 14px 22px; font-size: 14px; font-weight: 500; z-index: 999; white-space: nowrap; box-shadow: 0 8px 32px rgba(0,0,0,.5); animation: toastIn .3s ease; }
        @keyframes toastIn { from { opacity:0; transform: translateX(-50%) translateY(16px) } to { opacity:1; transform: translateX(-50%) translateY(0) } }
        @media (max-width:700px) { .hide-mobile { display:none !important; } }
      `}</style>

      {/* NAVBAR */}
      <nav style={{ position: "sticky", top: 0, zIndex: 50, background: "rgba(10,10,15,.9)", backdropFilter: "blur(12px)", borderBottom: "1px solid #1e1e2a", padding: "0 24px", height: 64, display: "flex", alignItems: "center", justifyContent: "space-between" }}>
        <div style={{ display: "flex", alignItems: "center", gap: 32 }}>
          <div onClick={() => setPage("home")} style={{ cursor: "pointer", display: "flex", alignItems: "center", gap: 10 }}>
            <div style={{ width: 34, height: 34, borderRadius: 10, background: "linear-gradient(135deg,#ff5a1f,#ff8c42)", display: "flex", alignItems: "center", justifyContent: "center", fontSize: 18 }}>🛍</div>
            <span style={{ fontFamily: "'Playfair Display', serif", fontSize: 22, fontWeight: 800, letterSpacing: "-0.5px" }}>ShopEZ</span>
          </div>
          <div className="hide-mobile" style={{ display: "flex", gap: 24 }}>
            {["home","shop","seller"].map(p => (
              <span key={p} className={`nav-link ${page === p ? "active" : ""}`} onClick={() => setPage(p)} style={{ textTransform: "capitalize" }}>{p === "seller" ? "Seller Hub" : p.charAt(0).toUpperCase() + p.slice(1)}</span>
            ))}
          </div>
        </div>
        <div style={{ display: "flex", alignItems: "center", gap: 16 }}>
          <div className="hide-mobile" style={{ position: "relative" }}>
            <input value={search} onChange={e => setSearch(e.target.value)} placeholder="Search products…" style={{ width: 220, paddingLeft: 36, height: 38, fontSize: 13 }} onClick={() => setPage("shop")} />
            <span style={{ position: "absolute", left: 12, top: "50%", transform: "translateY(-50%)", opacity: .5, fontSize: 14 }}>🔍</span>
          </div>
          <button onClick={() => toggleWishlist} style={{ background: "none", border: "none", cursor: "pointer", fontSize: 20, position: "relative", color: wishlist.length ? "#ff5a1f" : "#888" }} title="Wishlist">
            {wishlist.length > 0 ? "❤️" : "🤍"}
            {wishlist.length > 0 && <span className="badge">{wishlist.length}</span>}
          </button>
          <button onClick={() => setCartOpen(true)} style={{ background: "none", border: "none", cursor: "pointer", fontSize: 20, position: "relative" }}>
            🛒 {cartCount > 0 && <span className="badge">{cartCount}</span>}
          </button>
          <button className="hide-mobile btn-primary" style={{ padding: "8px 18px", fontSize: 13 }} onClick={() => showToast("Sign in coming soon!")}>Sign In</button>
        </div>
      </nav>

      {/* TOAST */}
      {toast && <div className="toast">✅ {toast.msg}</div>}

      {/* CART DRAWER */}
      {cartOpen && (
        <div style={{ position: "fixed", inset: 0, zIndex: 200 }}>
          <div onClick={() => setCartOpen(false)} style={{ position: "absolute", inset: 0, background: "rgba(0,0,0,.6)", backdropFilter: "blur(4px)" }} />
          <div className="drawer fade-in">
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 24 }}>
              <h2 style={{ fontFamily: "'Playfair Display', serif", fontSize: 22 }}>Your Cart {cartCount > 0 && <span style={{ fontSize: 14, color: "#ff5a1f" }}>({cartCount})</span>}</h2>
              <button onClick={() => setCartOpen(false)} style={{ background: "none", border: "none", color: "#888", fontSize: 22, cursor: "pointer" }}>✕</button>
            </div>
            {cart.length === 0 ? (
              <div style={{ textAlign: "center", padding: "60px 0", color: "#555" }}>
                <div style={{ fontSize: 48, marginBottom: 12 }}>🛒</div>
                <p>Your cart is empty</p>
                <button className="btn-primary" style={{ marginTop: 20 }} onClick={() => { setCartOpen(false); setPage("shop"); }}>Browse Products</button>
              </div>
            ) : (
              <>
                <div style={{ display: "flex", flexDirection: "column", gap: 14, marginBottom: 24 }}>
                  {cart.map(item => (
                    <div key={item.id} style={{ display: "flex", gap: 14, alignItems: "center", background: "#1c1c26", borderRadius: 14, padding: "12px 14px" }}>
                      <div style={{ fontSize: 32, width: 48, height: 48, display: "flex", alignItems: "center", justifyContent: "center", background: "#22222e", borderRadius: 10 }}>{item.image}</div>
                      <div style={{ flex: 1 }}>
                        <div style={{ fontWeight: 600, fontSize: 14 }}>{item.name}</div>
                        <div style={{ color: "#ff5a1f", fontSize: 14, marginTop: 2 }}>${item.price}</div>
                      </div>
                      <div style={{ display: "flex", alignItems: "center", gap: 8 }}>
                        <button onClick={() => updateQty(item.id, item.qty - 1)} style={{ width: 26, height: 26, border: "1px solid #333", background: "#14141c", borderRadius: 6, color: "#f0ece8", cursor: "pointer", fontSize: 14 }}>−</button>
                        <span style={{ minWidth: 20, textAlign: "center", fontSize: 14 }}>{item.qty}</span>
                        <button onClick={() => updateQty(item.id, item.qty + 1)} style={{ width: 26, height: 26, border: "1px solid #333", background: "#14141c", borderRadius: 6, color: "#f0ece8", cursor: "pointer", fontSize: 14 }}>+</button>
                      </div>
                      <button onClick={() => removeFromCart(item.id)} style={{ background: "none", border: "none", color: "#555", cursor: "pointer", fontSize: 16, marginLeft: 4 }}>🗑</button>
                    </div>
                  ))}
                </div>
                <div style={{ borderTop: "1px solid #22222e", paddingTop: 18, marginBottom: 20 }}>
                  <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, color: "#888", marginBottom: 6 }}>
                    <span>Subtotal</span><span>${cartTotal.toFixed(2)}</span>
                  </div>
                  <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, color: "#888", marginBottom: 6 }}>
                    <span>Shipping</span><span style={{ color: "#1af595" }}>Free</span>
                  </div>
                  <div style={{ display: "flex", justifyContent: "space-between", fontWeight: 700, fontSize: 17, marginTop: 10 }}>
                    <span>Total</span><span style={{ color: "#ff5a1f" }}>${cartTotal.toFixed(2)}</span>
                  </div>
                </div>
                <button className="btn-primary" style={{ width: "100%", fontSize: 16, padding: "14px" }} onClick={() => { setCartOpen(false); setPage("checkout"); }}>
                  Proceed to Checkout →
                </button>
              </>
            )}
          </div>
        </div>
      )}

      {/* PRODUCT MODAL */}
      {selectedProduct && (
        <div className="overlay" onClick={() => setSelectedProduct(null)}>
          <div className="modal fade-in" onClick={e => e.stopPropagation()}>
            <button onClick={() => setSelectedProduct(null)} style={{ position: "absolute", top: 16, right: 18, background: "none", border: "none", color: "#888", fontSize: 22, cursor: "pointer" }}>✕</button>
            <div style={{ fontSize: 72, textAlign: "center", marginBottom: 16, background: "#1c1c26", borderRadius: 16, padding: "24px 0" }}>{selectedProduct.image}</div>
            <div style={{ display: "flex", gap: 8, marginBottom: 10, flexWrap: "wrap" }}>
              <span className="tag tag-orange">{selectedProduct.tag}</span>
              <span className="tag tag-blue">{selectedProduct.category}</span>
              <span className="tag tag-green">-{selectedProduct.discount}% OFF</span>
            </div>
            <h2 style={{ fontFamily: "'Playfair Display', serif", fontSize: 26, marginBottom: 8 }}>{selectedProduct.name}</h2>
            <p style={{ color: "#a09d98", fontSize: 14, lineHeight: 1.7, marginBottom: 14 }}>{selectedProduct.description}</p>
            <div className="stars" style={{ marginBottom: 10 }}>{"★".repeat(Math.floor(selectedProduct.rating))} <span style={{ color: "#888", fontSize: 12 }}>({selectedProduct.reviews} reviews)</span></div>
            <div style={{ display: "flex", alignItems: "center", gap: 14, marginBottom: 22 }}>
              <span style={{ fontSize: 28, fontWeight: 800, color: "#ff5a1f" }}>${selectedProduct.price}</span>
              <span style={{ fontSize: 16, color: "#555", textDecoration: "line-through" }}>${selectedProduct.originalPrice}</span>
              <span style={{ fontSize: 13, color: "#1af595" }}>You save ${selectedProduct.originalPrice - selectedProduct.price}</span>
            </div>
            <div style={{ display: "flex", gap: 12 }}>
              <button className="btn-primary" style={{ flex: 1 }} onClick={() => { addToCart(selectedProduct); setSelectedProduct(null); }}>Add to Cart 🛒</button>
              <button className="btn-ghost" onClick={() => toggleWishlist(selectedProduct)}>{wishlist.find(w => w.id === selectedProduct.id) ? "❤️ Saved" : "🤍 Save"}</button>
            </div>
          </div>
        </div>
      )}

      {/* ORDER SUCCESS MODAL */}
      {orderPlaced && (
        <div className="overlay">
          <div className="modal fade-in" style={{ textAlign: "center" }}>
            <div style={{ fontSize: 64, marginBottom: 16 }}>🎉</div>
            <h2 style={{ fontFamily: "'Playfair Display', serif", fontSize: 28, marginBottom: 10 }}>Order Confirmed!</h2>
            <p style={{ color: "#a09d98", marginBottom: 8 }}>Your order <strong style={{ color: "#ff5a1f" }}>#EZ-{Math.floor(Math.random()*90000+10000)}</strong> has been placed successfully.</p>
            <p style={{ color: "#888", fontSize: 13, marginBottom: 24 }}>You'll receive an email confirmation shortly. Estimated delivery: 2–4 business days.</p>
            <div style={{ background: "#1af59511", border: "1px solid #1af59544", borderRadius: 12, padding: 16, marginBottom: 24, color: "#1af595", fontSize: 14 }}>
              ✅ Secure payment processed &nbsp;·&nbsp; 📦 Order dispatched soon
            </div>
            <button className="btn-primary" style={{ width: "100%" }} onClick={() => { setOrderPlaced(false); setPage("home"); }}>Back to Home</button>
          </div>
        </div>
      )}

      {/* ===== HOME PAGE ===== */}
      {page === "home" && (
        <div className="fade-in">
          {/* Hero */}
          <section style={{ padding: "80px 24px 60px", maxWidth: 1200, margin: "0 auto", display: "grid", gridTemplateColumns: "1fr 1fr", gap: 48, alignItems: "center" }} className="hide-mobile" ref={heroRef}>
            <div>
              <div style={{ display: "inline-flex", alignItems: "center", gap: 8, background: "#ff5a1f15", border: "1px solid #ff5a1f44", borderRadius: 30, padding: "6px 16px", marginBottom: 24, fontSize: 13, color: "#ff5a1f", fontWeight: 600 }}>
                ✨ New arrivals just dropped
              </div>
              <h1 style={{ fontFamily: "'Playfair Display', serif", fontSize: "clamp(38px,5vw,62px)", lineHeight: 1.1, fontWeight: 800, marginBottom: 20 }}>
                Shop Smarter,<br /><span style={{ background: "linear-gradient(135deg,#ff5a1f,#ff8c42)", WebkitBackgroundClip: "text", WebkitTextFillColor: "transparent" }}>Live Better</span>
              </h1>
              <p style={{ color: "#a09d98", fontSize: 17, lineHeight: 1.8, marginBottom: 32, maxWidth: 420 }}>
                Discover thousands of curated products with unbeatable deals, secure checkout, and fast delivery — all in one place.
              </p>
              <div style={{ display: "flex", gap: 14 }}>
                <button className="btn-primary" style={{ fontSize: 16, padding: "14px 28px" }} onClick={() => setPage("shop")}>Start Shopping →</button>
                <button className="btn-ghost" onClick={() => setPage("seller")}>Sell on ShopEZ</button>
              </div>
              <div style={{ display: "flex", gap: 32, marginTop: 40 }}>
                {[["50K+","Products"],["2M+","Happy Buyers"],["4.9★","Avg Rating"]].map(([v,l]) => (
                  <div key={l}><div style={{ fontSize: 22, fontWeight: 800, color: "#ff5a1f" }}>{v}</div><div style={{ fontSize: 12, color: "#666", marginTop: 2 }}>{l}</div></div>
                ))}
              </div>
            </div>
            <div className="hero-card" style={{ position: "relative" }}>
              <div style={{ background: "linear-gradient(135deg,#1a1a28,#22222e)", borderRadius: 28, padding: 32, border: "1px solid #2e2e3e", boxShadow: "0 32px 80px rgba(255,90,31,.12)" }}>
                <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 14 }}>
                  {products.slice(0,4).map(p => (
                    <div key={p.id} onClick={() => { setSelectedProduct(p); }} style={{ background: "#0e0e18", borderRadius: 16, padding: 16, cursor: "pointer", border: "1px solid #22222e", transition: "all .2s" }}
                      onMouseEnter={e => e.currentTarget.style.borderColor = "#ff5a1f55"}
                      onMouseLeave={e => e.currentTarget.style.borderColor = "#22222e"}>
                      <div style={{ fontSize: 34, marginBottom: 8 }}>{p.image}</div>
                      <div style={{ fontSize: 13, fontWeight: 600, marginBottom: 4 }}>{p.name}</div>
                      <div style={{ fontSize: 13, color: "#ff5a1f", fontWeight: 700 }}>${p.price}</div>
                    </div>
                  ))}
                </div>
              </div>
            </div>
          </section>

          {/* Mobile Hero */}
          <section className="hide-desktop" style={{ padding: "48px 20px 32px", textAlign: "center", display: "none" }}>
            <h1 style={{ fontFamily: "'Playfair Display', serif", fontSize: 36, fontWeight: 800, marginBottom: 14 }}>Shop Smarter,<br /><span style={{ color: "#ff5a1f" }}>Live Better</span></h1>
            <p style={{ color: "#a09d98", marginBottom: 24 }}>Discover thousands of curated products with unbeatable deals.</p>
            <button className="btn-primary" onClick={() => setPage("shop")}>Start Shopping →</button>
          </section>

          {/* Categories */}
          <section style={{ padding: "48px 24px", maxWidth: 1200, margin: "0 auto" }}>
            <h2 style={{ fontFamily: "'Playfair Display', serif", fontSize: 28, marginBottom: 24 }}>Shop by Category</h2>
            <div style={{ display: "flex", gap: 14, flexWrap: "wrap" }}>
              {[["Electronics","📱"],["Fashion","👗"],["Home","🏠"],["Beauty","💄"],["Kitchen","🍳"]].map(([cat, icon]) => (
                <div key={cat} onClick={() => { setCategory(cat); setPage("shop"); }}
                  style={{ background: "#14141c", border: "1px solid #22222e", borderRadius: 16, padding: "16px 24px", cursor: "pointer", display: "flex", alignItems: "center", gap: 10, transition: "all .2s", minWidth: 140 }}
                  onMouseEnter={e => { e.currentTarget.style.borderColor = "#ff5a1f"; e.currentTarget.style.background = "#1a1a26"; }}
                  onMouseLeave={e => { e.currentTarget.style.borderColor = "#22222e"; e.currentTarget.style.background = "#14141c"; }}>
                  <span style={{ fontSize: 24 }}>{icon}</span>
                  <span style={{ fontWeight: 600, fontSize: 15 }}>{cat}</span>
                </div>
              ))}
            </div>
          </section>

          {/* Featured Products */}
          <section style={{ padding: "0 24px 60px", maxWidth: 1200, margin: "0 auto" }}>
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 28 }}>
              <h2 style={{ fontFamily: "'Playfair Display', serif", fontSize: 28 }}>Featured Deals</h2>
              <button className="btn-ghost" style={{ padding: "8px 18px", fontSize: 13 }} onClick={() => setPage("shop")}>View All →</button>
            </div>
            <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill,minmax(240px,1fr))", gap: 20 }}>
              {products.slice(0,4).map(p => (
                <div key={p.id} className="card" style={{ cursor: "pointer" }} onClick={() => setSelectedProduct(p)}>
                  <div style={{ background: "#1c1c26", padding: "32px 0", textAlign: "center", fontSize: 56 }}>{p.image}</div>
                  <div style={{ padding: "18px 18px 20px" }}>
                    <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 8 }}>
                      <span className="tag tag-orange">{p.tag}</span>
                      <span style={{ fontSize: 12, color: "#1af595", fontWeight: 600 }}>-{p.discount}%</span>
                    </div>
                    <h3 style={{ fontWeight: 700, fontSize: 16, marginBottom: 4 }}>{p.name}</h3>
                    <div className="stars" style={{ marginBottom: 8, fontSize: 12 }}>{"★".repeat(Math.floor(p.rating))} <span style={{ color: "#888", fontSize: 11 }}>({p.reviews})</span></div>
                    <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center" }}>
                      <div>
                        <span style={{ fontSize: 18, fontWeight: 800, color: "#ff5a1f" }}>${p.price}</span>
                        <span style={{ fontSize: 12, color: "#555", textDecoration: "line-through", marginLeft: 6 }}>${p.originalPrice}</span>
                      </div>
                      <button className="btn-primary" style={{ padding: "7px 14px", fontSize: 12 }} onClick={e => { e.stopPropagation(); addToCart(p); }}>Add</button>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          </section>

          {/* Reviews */}
          <section style={{ padding: "0 24px 80px", maxWidth: 1200, margin: "0 auto" }}>
            <h2 style={{ fontFamily: "'Playfair Display', serif", fontSize: 28, marginBottom: 28, textAlign: "center" }}>What Customers Say</h2>
            <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill,minmax(300px,1fr))", gap: 20 }}>
              {reviews.map((r, i) => (
                <div key={i} className="card" style={{ padding: 24 }}>
                  <div className="stars" style={{ marginBottom: 12 }}>{"★".repeat(r.rating)}</div>
                  <p style={{ color: "#c0bbb5", lineHeight: 1.7, fontSize: 14, marginBottom: 18 }}>"{r.text}"</p>
                  <div style={{ display: "flex", alignItems: "center", gap: 12 }}>
                    <div style={{ width: 40, height: 40, borderRadius: "50%", background: "linear-gradient(135deg,#ff5a1f,#ff8c42)", display: "flex", alignItems: "center", justifyContent: "center", fontWeight: 700, fontSize: 16 }}>{r.avatar}</div>
                    <div>
                      <div style={{ fontWeight: 600, fontSize: 14 }}>{r.user}</div>
                      <div style={{ color: "#666", fontSize: 12 }}>Bought: {r.product}</div>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          </section>
        </div>
      )}

      {/* ===== SHOP PAGE ===== */}
      {page === "shop" && (
        <div className="fade-in" style={{ maxWidth: 1200, margin: "0 auto", padding: "40px 24px" }}>
          <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 32, flexWrap: "wrap", gap: 16 }}>
            <div>
              <h1 style={{ fontFamily: "'Playfair Display', serif", fontSize: 32 }}>All Products</h1>
              <p style={{ color: "#666", marginTop: 4, fontSize: 14 }}>{filtered.length} items found</p>
            </div>
            <input value={search} onChange={e => setSearch(e.target.value)} placeholder="🔍  Search products…" style={{ width: 260 }} />
          </div>
          <div style={{ display: "flex", gap: 10, marginBottom: 32, flexWrap: "wrap" }}>
            {categories.map(cat => (
              <button key={cat} onClick={() => setCategory(cat)} style={{ background: category === cat ? "linear-gradient(135deg,#ff5a1f,#ff8c42)" : "#14141c", border: `1.5px solid ${category === cat ? "transparent" : "#2e2e3e"}`, borderRadius: 30, padding: "8px 18px", fontSize: 13, fontWeight: 600, cursor: "pointer", color: "#f0ece8", transition: "all .2s" }}>
                {cat}
              </button>
            ))}
          </div>
          {filtered.length === 0 ? (
            <div style={{ textAlign: "center", padding: "80px 0", color: "#555" }}>
              <div style={{ fontSize: 48, marginBottom: 12 }}>🔍</div>
              <p>No products found for "{search}"</p>
            </div>
          ) : (
            <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill,minmax(240px,1fr))", gap: 22 }}>
              {filtered.map(p => (
                <div key={p.id} className="card" style={{ cursor: "pointer" }} onClick={() => setSelectedProduct(p)}>
                  <div style={{ background: "#1c1c26", padding: "32px 0", textAlign: "center", fontSize: 56, position: "relative" }}>
                    {p.image}
                    <div style={{ position: "absolute", top: 12, right: 12 }}>
                      <button onClick={e => { e.stopPropagation(); toggleWishlist(p); }} style={{ background: "#14141c", border: "none", borderRadius: "50%", width: 32, height: 32, cursor: "pointer", fontSize: 16, display: "flex", alignItems: "center", justifyContent: "center" }}>
                        {wishlist.find(w => w.id === p.id) ? "❤️" : "🤍"}
                      </button>
                    </div>
                  </div>
                  <div style={{ padding: "16px 16px 18px" }}>
                    <div style={{ display: "flex", gap: 6, marginBottom: 8 }}>
                      <span className="tag tag-blue">{p.category}</span>
                      <span className="tag tag-green">-{p.discount}%</span>
                    </div>
                    <h3 style={{ fontWeight: 700, fontSize: 15, marginBottom: 4 }}>{p.name}</h3>
                    <p style={{ color: "#666", fontSize: 12, marginBottom: 8, lineHeight: 1.5 }}>{p.description.slice(0, 55)}…</p>
                    <div className="stars" style={{ marginBottom: 10, fontSize: 12 }}>{"★".repeat(Math.floor(p.rating))} <span style={{ color: "#888", fontSize: 11 }}>({p.reviews})</span></div>
                    <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center" }}>
                      <div>
                        <span style={{ fontSize: 18, fontWeight: 800, color: "#ff5a1f" }}>${p.price}</span>
                        <span style={{ fontSize: 12, color: "#555", textDecoration: "line-through", marginLeft: 6 }}>${p.originalPrice}</span>
                      </div>
                      <button className="btn-primary" style={{ padding: "7px 14px", fontSize: 12 }} onClick={e => { e.stopPropagation(); addToCart(p); }}>Add 🛒</button>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          )}
        </div>
      )}

      {/* ===== CHECKOUT PAGE ===== */}
      {page === "checkout" && (
        <div className="fade-in" style={{ maxWidth: 900, margin: "0 auto", padding: "48px 24px" }}>
          <h1 style={{ fontFamily: "'Playfair Display', serif", fontSize: 32, marginBottom: 32 }}>Checkout</h1>
          {/* Steps */}
          <div style={{ display: "flex", alignItems: "center", marginBottom: 40, gap: 0 }}>
            {[["1","Shipping"],["2","Payment"],["3","Review"]].map(([n, label], i) => (
              <div key={n} style={{ display: "flex", alignItems: "center", flex: 1 }}>
                <div className={`progress-step ${checkoutStep > i ? "done" : checkoutStep === i+1 ? "active" : ""}`} style={{ flexDirection: "column", gap: 4, minWidth: 64 }}>
                  <div className="step-circle">{checkoutStep > i+1 ? "✓" : n}</div>
                  <span style={{ fontSize: 11 }}>{label}</span>
                </div>
                {i < 2 && <div style={{ flex: 1, height: 2, background: checkoutStep > i+1 ? "#1af595" : "#2e2e3e", margin: "0 8px", marginBottom: 18 }} />}
              </div>
            ))}
          </div>

          <div style={{ display: "grid", gridTemplateColumns: "1fr 360px", gap: 32 }}>
            <div>
              {checkoutStep === 1 && (
                <div className="card" style={{ padding: 28 }}>
                  <h3 style={{ marginBottom: 20, fontWeight: 700 }}>Shipping Information</h3>
                  <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 14 }}>
                    {[["First Name","text"],["Last Name","text"],["Email","email"],["Phone","tel"]].map(([ph, type]) => (
                      <input key={ph} placeholder={ph} type={type} style={{ width: "100%" }} />
                    ))}
                    <input placeholder="Street Address" style={{ gridColumn: "1/-1", width: "100%" }} />
                    {[["City",""],["ZIP Code",""],["State",""]].map(([ph]) => (
                      <input key={ph} placeholder={ph} style={{ width: "100%" }} />
                    ))}
                  </div>
                  <button className="btn-primary" style={{ marginTop: 20, width: "100%", padding: 14 }} onClick={() => setCheckoutStep(2)}>Continue to Payment →</button>
                </div>
              )}
              {checkoutStep === 2 && (
                <div className="card" style={{ padding: 28 }}>
                  <h3 style={{ marginBottom: 20, fontWeight: 700 }}>Payment Details</h3>
                  <div style={{ display: "flex", gap: 10, marginBottom: 20 }}>
                    {["💳 Card","🏦 UPI","💰 Wallet"].map(m => (
                      <button key={m} className="btn-ghost" style={{ flex: 1, fontSize: 13 }}>{m}</button>
                    ))}
                  </div>
                  <input placeholder="Cardholder Name" style={{ width: "100%", marginBottom: 14 }} />
                  <input placeholder="Card Number (1234 5678 9012 3456)" style={{ width: "100%", marginBottom: 14 }} />
                  <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 14 }}>
                    <input placeholder="MM/YY" />
                    <input placeholder="CVV" />
                  </div>
                  <div style={{ display: "flex", gap: 12, marginTop: 20 }}>
                    <button className="btn-ghost" onClick={() => setCheckoutStep(1)}>← Back</button>
                    <button className="btn-primary" style={{ flex: 1, padding: 14 }} onClick={() => setCheckoutStep(3)}>Review Order →</button>
                  </div>
                </div>
              )}
              {checkoutStep === 3 && (
                <div className="card" style={{ padding: 28 }}>
                  <h3 style={{ marginBottom: 20, fontWeight: 700 }}>Review Your Order</h3>
                  {cart.map(item => (
                    <div key={item.id} style={{ display: "flex", gap: 14, alignItems: "center", marginBottom: 14, paddingBottom: 14, borderBottom: "1px solid #1e1e2a" }}>
                      <div style={{ fontSize: 30, background: "#1c1c26", borderRadius: 10, width: 48, height: 48, display: "flex", alignItems: "center", justifyContent: "center" }}>{item.image}</div>
                      <div style={{ flex: 1 }}>
                        <div style={{ fontWeight: 600 }}>{item.name}</div>
                        <div style={{ color: "#888", fontSize: 13 }}>Qty: {item.qty}</div>
                      </div>
                      <div style={{ fontWeight: 700, color: "#ff5a1f" }}>${(item.price * item.qty).toFixed(2)}</div>
                    </div>
                  ))}
                  <div style={{ background: "#1af59511", border: "1px solid #1af59544", borderRadius: 12, padding: 14, marginTop: 16, marginBottom: 20, fontSize: 13, color: "#1af595" }}>
                    🔒 Your payment is secured with 256-bit SSL encryption
                  </div>
                  <div style={{ display: "flex", gap: 12 }}>
                    <button className="btn-ghost" onClick={() => setCheckoutStep(2)}>← Back</button>
                    <button className="btn-primary" style={{ flex: 1, padding: 14, fontSize: 16 }} onClick={placeOrder}>Place Order 🎉</button>
                  </div>
                </div>
              )}
            </div>

            {/* Order Summary */}
            <div className="card" style={{ padding: 24, height: "fit-content" }}>
              <h3 style={{ marginBottom: 18, fontWeight: 700 }}>Order Summary</h3>
              {cart.slice(0,3).map(item => (
                <div key={item.id} style={{ display: "flex", justifyContent: "space-between", marginBottom: 10, fontSize: 13 }}>
                  <span style={{ color: "#a09d98" }}>{item.name} × {item.qty}</span>
                  <span>${(item.price * item.qty).toFixed(2)}</span>
                </div>
              ))}
              {cart.length > 3 && <p style={{ color: "#666", fontSize: 12, marginBottom: 10 }}>+{cart.length - 3} more items</p>}
              <div style={{ borderTop: "1px solid #22222e", paddingTop: 14, marginTop: 6 }}>
                <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, color: "#888", marginBottom: 6 }}><span>Subtotal</span><span>${cartTotal.toFixed(2)}</span></div>
                <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, color: "#888", marginBottom: 6 }}><span>Shipping</span><span style={{ color: "#1af595" }}>FREE</span></div>
                <div style={{ display: "flex", justifyContent: "space-between", fontSize: 18, fontWeight: 800, marginTop: 10 }}><span>Total</span><span style={{ color: "#ff5a1f" }}>${cartTotal.toFixed(2)}</span></div>
              </div>
            </div>
          </div>
        </div>
      )}

      {/* ===== SELLER HUB ===== */}
      {page === "seller" && (
        <div className="fade-in" style={{ maxWidth: 1200, margin: "0 auto", padding: "48px 24px" }}>
          <div style={{ marginBottom: 40 }}>
            <div style={{ display: "inline-flex", alignItems: "center", gap: 8, background: "#1af59511", border: "1px solid #1af59544", borderRadius: 30, padding: "6px 16px", marginBottom: 16, fontSize: 13, color: "#1af595", fontWeight: 600 }}>
              📊 Seller Dashboard
            </div>
            <h1 style={{ fontFamily: "'Playfair Display', serif", fontSize: 36, marginBottom: 8 }}>Welcome back, <span style={{ color: "#ff5a1f" }}>Alex</span></h1>
            <p style={{ color: "#888" }}>Here's how your store is performing today</p>
          </div>

          {/* Stats */}
          <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit,minmax(200px,1fr))", gap: 18, marginBottom: 40 }}>
            {sellerStats.map(stat => (
              <div key={stat.label} className="stat-card">
                <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginBottom: 12 }}>
                  <span style={{ fontSize: 28 }}>{stat.icon}</span>
                  <span style={{ background: "#1af59511", color: "#1af595", border: "1px solid #1af59544", borderRadius: 20, padding: "3px 10px", fontSize: 12, fontWeight: 600 }}>{stat.change}</span>
                </div>
                <div style={{ fontSize: 26, fontWeight: 800, marginBottom: 4 }}>{stat.value}</div>
                <div style={{ color: "#666", fontSize: 13 }}>{stat.label}</div>
              </div>
            ))}
          </div>

          <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 24 }}>
            {/* Recent Orders */}
            <div className="card" style={{ padding: 24 }}>
              <h3 style={{ fontWeight: 700, marginBottom: 20, fontFamily: "'Playfair Display', serif", fontSize: 20 }}>Recent Orders</h3>
              {[
                { id: "#EZ-94821", product: "Aura Wireless Buds", customer: "Priya S.", status: "Shipped", amount: "$89" },
                { id: "#EZ-94756", product: "CloudStep Sneakers", customer: "James K.", status: "Processing", amount: "$129" },
                { id: "#EZ-94690", product: "Ember Smartwatch", customer: "Maya R.", status: "Delivered", amount: "$249" },
                { id: "#EZ-94633", product: "Chef's Knife Set", customer: "Tom W.", status: "Processing", amount: "$119" },
              ].map(order => (
                <div key={order.id} style={{ display: "flex", justifyContent: "space-between", alignItems: "center", padding: "12px 0", borderBottom: "1px solid #1e1e2a" }}>
                  <div>
                    <div style={{ fontWeight: 600, fontSize: 14 }}>{order.product}</div>
                    <div style={{ color: "#666", fontSize: 12 }}>{order.id} · {order.customer}</div>
                  </div>
                  <div style={{ textAlign: "right" }}>
                    <div style={{ fontWeight: 700, color: "#ff5a1f" }}>{order.amount}</div>
                    <span className={`tag ${order.status === "Delivered" ? "tag-green" : order.status === "Shipped" ? "tag-blue" : "tag-orange"}`} style={{ fontSize: 10 }}>{order.status}</span>
                  </div>
                </div>
              ))}
            </div>

            {/* Analytics */}
            <div className="card" style={{ padding: 24 }}>
              <h3 style={{ fontWeight: 700, marginBottom: 20, fontFamily: "'Playfair Display', serif", fontSize: 20 }}>Weekly Revenue</h3>
              <div style={{ display: "flex", alignItems: "flex-end", gap: 10, height: 140, marginBottom: 8 }}>
                {[60,80,45,90,70,100,85].map((h, i) => (
                  <div key={i} style={{ flex: 1, display: "flex", flexDirection: "column", alignItems: "center", gap: 4 }}>
                    <div style={{ width: "100%", height: `${h}%`, background: i === 5 ? "linear-gradient(180deg,#ff5a1f,#ff8c42)" : "#22222e", borderRadius: "4px 4px 0 0", transition: "all .3s" }}
                      onMouseEnter={e => e.currentTarget.style.background = "linear-gradient(180deg,#ff5a1f,#ff8c42)"}
                      onMouseLeave={e => { if (i !== 5) e.currentTarget.style.background = "#22222e"; }} />
                  </div>
                ))}
              </div>
              <div style={{ display: "flex", gap: 10, justifyContent: "space-around" }}>
                {["Mon","Tue","Wed","Thu","Fri","Sat","Sun"].map(d => (
                  <span key={d} style={{ fontSize: 11, color: "#555", flex: 1, textAlign: "center" }}>{d}</span>
                ))}
              </div>
              <div style={{ marginTop: 24, padding: 16, background: "#1c1c26", borderRadius: 12 }}>
                <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 8, fontSize: 13, color: "#888" }}>
                  <span>Best Day</span><span style={{ color: "#1af595" }}>Saturday (+$8,200)</span>
                </div>
                <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, color: "#888" }}>
                  <span>Avg. Order Value</span><span style={{ color: "#f0ece8" }}>$169</span>
                </div>
              </div>
            </div>
          </div>

          {/* Add Product CTA */}
          <div style={{ marginTop: 28, background: "linear-gradient(135deg,#ff5a1f15,#1a1a26)", border: "1px solid #ff5a1f33", borderRadius: 20, padding: "28px 32px", display: "flex", justifyContent: "space-between", alignItems: "center", flexWrap: "wrap", gap: 16 }}>
            <div>
              <h3 style={{ fontFamily: "'Playfair Display', serif", fontSize: 22, marginBottom: 6 }}>Ready to list a new product?</h3>
              <p style={{ color: "#888", fontSize: 14 }}>Reach 2M+ active buyers on ShopEZ today.</p>
            </div>
            <button className="btn-primary" style={{ fontSize: 15, padding: "13px 28px" }} onClick={() => showToast("Product listing coming soon!")}>+ Add New Product</button>
          </div>
        </div>
      )}

      {/* FOOTER */}
      <footer style={{ borderTop: "1px solid #1e1e2a", padding: "48px 24px 32px", maxWidth: 1200, margin: "0 auto" }}>
        <div style={{ display: "grid", gridTemplateColumns: "2fr 1fr 1fr 1fr", gap: 32, marginBottom: 40 }}>
          <div>
            <div style={{ display: "flex", alignItems: "center", gap: 10, marginBottom: 14 }}>
              <div style={{ width: 34, height: 34, borderRadius: 10, background: "linear-gradient(135deg,#ff5a1f,#ff8c42)", display: "flex", alignItems: "center", justifyContent: "center", fontSize: 18 }}>🛍</div>
              <span style={{ fontFamily: "'Playfair Display', serif", fontSize: 20, fontWeight: 800 }}>ShopEZ</span>
            </div>
            <p style={{ color: "#666", fontSize: 14, lineHeight: 1.8, maxWidth: 280 }}>Your one-stop destination for effortless online shopping. Quality products, unbeatable prices.</p>
          </div>
          {[["Shop",["Electronics","Fashion","Home","Beauty"]],["Company",["About Us","Careers","Press","Blog"]],["Support",["Help Center","Returns","Track Order","Contact"]]].map(([title, links]) => (
            <div key={title}>
              <h4 style={{ fontWeight: 700, marginBottom: 14, fontSize: 14 }}>{title}</h4>
              {links.map(l => <div key={l} style={{ color: "#666", fontSize: 13, marginBottom: 8, cursor: "pointer", transition: "color .2s" }}
                onMouseEnter={e => e.target.style.color = "#ff5a1f"} onMouseLeave={e => e.target.style.color = "#666"}>{l}</div>)}
            </div>
          ))}
        </div>
        <div style={{ borderTop: "1px solid #1e1e2a", paddingTop: 24, display: "flex", justifyContent: "space-between", alignItems: "center", flexWrap: "wrap", gap: 12 }}>
          <span style={{ color: "#555", fontSize: 13 }}>© 2025 ShopEZ. All rights reserved.</span>
          <div style={{ display: "flex", gap: 16 }}>
            {["🔒 Secure Checkout","📦 Free Shipping","↩️ Easy Returns"].map(item => (
              <span key={item} style={{ fontSize: 12, color: "#555" }}>{item}</span>
            ))}
          </div>
        </div>
      </footer>
    </div>
  );
}
