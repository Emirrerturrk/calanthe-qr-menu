// --- Component ---

"use client";

import React, { useState, useEffect, useCallback, useRef } from "react";

// Inline Icons (Zero external dependencies)
const ChevronLeftIcon = () => (
  <svg width="22" height="22" fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2.5}>
    <path strokeLinecap="round" strokeLinejoin="round" d="M15 19l-7-7 7-7" />
  </svg>
);

const ChevronRightIcon = () => (
  <svg width="22" height="22" fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2.5}>
    <path strokeLinecap="round" strokeLinejoin="round" d="M9 5l7 7-7 7" />
  </svg>
);

const ArrowRightIcon = () => (
  <svg width="13" height="13" fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2.5}>
    <path strokeLinecap="round" strokeLinejoin="round" d="M14 5l7 7m0 0l-7 7m7-7H3" />
  </svg>
);

export interface CarouselItem {
  tag?: string;
  titleLine1: string;
  titleLine2?: string;
  desc?: string;
  img: string;
  ctaText?: string;
  ctaUrl?: string;
}

export interface CoverFlowCarouselProps {
  items?: CarouselItem[];
  brandTitle?: string;
  sectionLabel?: string;
  autoplay?: boolean;
  autoplayDelay?: number;
  className?: string;
  onCtaClick?: (item: CarouselItem) => void;
}

export const defaultDishes: CarouselItem[] = [
  {
    tag: "#SignatureBreakfast",
    titleLine1: "EGG BENEDICT",
    titleLine2: "– HOLLANDEZ & AVOKADO",
    desc: "Poşe yumurta, füme antrikot, hollandez sos, ekşi mayalı ekmek ve patates bravas",
    img: "./images/egg_benedict.webp",
    ctaText: "Menüyü İncele",
    ctaUrl: "#0",
  },
  {
    tag: "#ChefSpecial",
    titleLine1: "KATSU-SANDO",
    titleLine2: "– ÇITIR TAVUK BRİOCHE",
    desc: "Panelenmiş çıtır tavuk, brioche tost ekmeği, patates bravas, coleslaw ve tenkatsu sos",
    img: "./images/katsu_sando.webp",
    ctaText: "Menüyü İncele",
    ctaUrl: "#1",
  },
  {
    tag: "#HealthyChoice",
    titleLine1: "POKE BOWL",
    titleLine2: "– IZGARA SOMON",
    desc: "Izgara somon, taze avokado, kinoa, edamame, baby turp, susam ve yeşillik mix",
    img: "./images/salmon_poke_bowl.webp",
    ctaText: "Menüyü İncele",
    ctaUrl: "#1",
  },
  {
    tag: "#SweetSignature",
    titleLine1: "PLATO FELIZ",
    titleLine2: "– BELÇİKA ÇİKOLATASI",
    desc: "Özel choux hamuru, Calanthe krema, Belçika çikolatası, taze muz ve çilek",
    img: "./images/plato_feliz.webp",
    ctaText: "Menüyü İncele",
    ctaUrl: "#2",
  },
  {
    tag: "#ArtisanDessert",
    titleLine1: "FISTIKLI TİRAMİSU",
    titleLine2: "– ANTEP FISTIĞI DOKUNUŞU",
    desc: "Espresso ile demlenmiş savoiardi, mascarpone tiramisu kreması ve yoğun Antep fıstığı",
    img: "./images/pistachio_tiramisu.webp",
    ctaText: "Menüyü İncele",
    ctaUrl: "#2",
  },
];

export function CoverFlowCarousel({
  items = defaultDishes,
  brandTitle = "CALANTHE",
  sectionLabel = "ÖNE ÇIKAN LEZZETLER",
  autoplay = true,
  autoplayDelay = 5000,
  className = "",
  onCtaClick,
}: CoverFlowCarouselProps) {
  const [currentIndex, setCurrentIndex] = useState(0);
  const [isHovered, setIsHovered] = useState(false);
  const [windowWidth, setWindowWidth] = useState(typeof window !== "undefined" ? window.innerWidth : 1200);
  const touchStartX = useRef(0);
  const total = items.length;

  useEffect(() => {
    const handleResize = () => setWindowWidth(window.innerWidth);
    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  const nextSlide = useCallback(() => {
    setCurrentIndex((prev) => (prev + 1) % total);
  }, [total]);

  const prevSlide = useCallback(() => {
    setCurrentIndex((prev) => (prev - 1 + total) % total);
  }, [total]);

  const goToSlide = (idx: number) => {
    setCurrentIndex(idx % total);
  };

  useEffect(() => {
    if (!autoplay || isHovered || total <= 1) return;
    const interval = setInterval(nextSlide, autoplayDelay);
    return () => clearInterval(interval);
  }, [autoplay, autoplayDelay, isHovered, nextSlide, total]);

  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key === "ArrowLeft") prevSlide();
      if (e.key === "ArrowRight") nextSlide();
    };
    window.addEventListener("keydown", handleKeyDown);
    return () => window.removeEventListener("keydown", handleKeyDown);
  }, [nextSlide, prevSlide]);

  const handleTouchStart = (e: React.TouchEvent) => {
    touchStartX.current = e.touches[0].clientX;
  };

  const handleTouchEnd = (e: React.TouchEvent) => {
    const diff = e.changedTouches[0].clientX - touchStartX.current;
    if (Math.abs(diff) > 40) {
      if (diff < 0) nextSlide();
      else prevSlide();
    }
  };

  if (!items || items.length === 0) return null;

  const isMobile = windowWidth < 640;
  const isTablet = windowWidth >= 640 && windowWidth < 1024;

  let xOffset1 = 285;
  let xOffset2 = 510;
  let scale1 = 0.84;
  let scale2 = 0.68;
  let rot1 = 24;
  let rot2 = 38;

  if (isMobile) {
    xOffset1 = Math.min(windowWidth * 0.44, 150);
    xOffset2 = xOffset1 * 1.5;
    scale1 = 0.82;
    scale2 = 0.65;
    rot1 = 18;
    rot2 = 28;
  } else if (isTablet) {
    xOffset1 = Math.min(windowWidth * 0.32, 220);
    xOffset2 = xOffset1 * 1.8;
    scale1 = 0.83;
    scale2 = 0.66;
    rot1 = 22;
    rot2 = 34;
  }

  return (
    <section
      className={`relative w-full h-screen h-[100dvh] min-h-screen min-h-[100dvh] flex flex-col items-center justify-center overflow-hidden py-4 sm:py-6 select-none ${className}`}
      style={{
        backgroundColor: "#0c0a09",
        color: "#ffffff",
        fontFamily: "system-ui, -apple-system, sans-serif",
      }}
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
      onTouchStart={handleTouchStart}
      onTouchEnd={handleTouchEnd}
    >
      {/* Background Ambience */}
      <div className="absolute inset-0 overflow-hidden pointer-events-none z-0">
        <img
          src={items[currentIndex]?.img}
          alt="ambience background"
          style={{
            width: "100%",
            height: "100%",
            objectFit: "cover",
            filter: "brightness(0.24) blur(36px)",
            transform: "scale(1.18)",
            transition: "opacity 800ms ease, filter 800ms ease",
          }}
        />
        <div
          className="absolute inset-0"
          style={{
            background: "radial-gradient(circle at center, rgba(12,10,9,0.18) 0%, rgba(12,10,9,0.96) 100%)",
          }}
        />
      </div>

      <div className="relative w-full max-w-6xl h-full mx-auto px-3 sm:px-4 z-10 flex flex-col items-center justify-evenly">
        {/* Brand Header */}
        <div className="flex flex-col items-center gap-1">
          {brandTitle && (
            <h1
              style={{
                fontFamily: "Poppins, sans-serif",
                fontSize: isMobile ? "1.28rem" : "1.6rem",
                fontWeight: 900,
                letterSpacing: isMobile ? "0.14em" : "0.18em",
                textTransform: "uppercase",
                background: "linear-gradient(135deg, #ffffff 0%, #c5a880 100%)",
                WebkitBackgroundClip: "text",
                WebkitTextFillColor: "transparent",
                margin: 0,
                lineHeight: 1,
              }}
            >
              {brandTitle}
            </h1>
          )}
          {sectionLabel && (
            <div className="flex items-center gap-2 sm:gap-3">
              <span style={{ width: isMobile ? "24px" : "32px", height: "1px", background: "linear-gradient(90deg, transparent, #c5a880)" }} />
              <span
                style={{
                  fontSize: isMobile ? "0.68rem" : "0.74rem",
                  fontWeight: 700,
                  letterSpacing: isMobile ? "0.2em" : "0.28em",
                  textTransform: "uppercase",
                  color: "#c5a880",
                }}
              >
                {sectionLabel}
              </span>
              <span style={{ width: isMobile ? "24px" : "32px", height: "1px", background: "linear-gradient(90deg, #c5a880, transparent)" }} />
            </div>
          )}
        </div>

        {/* 3D Coverflow Stage */}
        <div
          className="relative w-full flex justify-center items-center"
          style={{
            height: isMobile ? "380px" : isTablet ? "460px" : "510px",
            perspective: isMobile ? "900px" : "1400px",
          }}
        >
          {items.map((item, idx) => {
            const offset = (idx - currentIndex + total) % total;

            let transform = "translateX(0px) scale(0.4) rotateY(0deg)";
            let opacity = 0;
            let zIndex = 0;
            let filter = "brightness(0.4) blur(2px)";
            let isCenter = false;

            if (offset === 0) {
              isCenter = true;
              transform = "translateX(0px) scale(1) rotateY(0deg)";
              opacity = 1;
              zIndex = 30;
              filter = "brightness(1)";
            } else if (offset === 1) {
              transform = `translateX(${xOffset1}px) scale(${scale1}) rotateY(-${rot1}deg)`;
              opacity = isMobile ? 0.55 : 0.65;
              zIndex = 20;
              filter = "brightness(0.75)";
            } else if (offset === 2) {
              transform = `translateX(${xOffset2}px) scale(${scale2}) rotateY(-${rot2}deg)`;
              opacity = isMobile ? 0 : 0.38;
              zIndex = 10;
              filter = "brightness(0.55) blur(1px)";
            } else if (offset === total - 1) {
              transform = `translateX(-${xOffset1}px) scale(${scale1}) rotateY(${rot1}deg)`;
              opacity = isMobile ? 0.55 : 0.65;
              zIndex = 20;
              filter = "brightness(0.75)";
            } else if (offset === total - 2) {
              transform = `translateX(-${xOffset2}px) scale(${scale2}) rotateY(${rot2}deg)`;
              opacity = isMobile ? 0 : 0.38;
              zIndex = 10;
              filter = "brightness(0.55) blur(1px)";
            }

            return (
              <div
                key={idx}
                onClick={() => !isCenter && goToSlide(idx)}
                style={{
                  position: "absolute",
                  width: isMobile ? "min(260px, 75vw)" : isTablet ? "295px" : "330px",
                  height: isMobile ? "370px" : isTablet ? "450px" : "490px",
                  borderRadius: isMobile ? "16px" : "20px",
                  overflow: "hidden",
                  backgroundColor: "#171311",
                  border: "1px solid rgba(255, 255, 255, 0.14)",
                  transform,
                  opacity,
                  zIndex,
                  filter,
                  transformOrigin: "center center",
                  transition: "all 700ms cubic-bezier(0.22, 1, 0.36, 1)",
                  boxShadow: isCenter
                    ? "0 20px 50px rgba(0,0,0,0.9), 0 0 30px rgba(197,168,128,0.25)"
                    : "0 10px 25px rgba(0,0,0,0.5)",
                  cursor: isCenter ? "default" : "pointer",
                }}
              >
                {/* Photo */}
                <img
                  src={item.img}
                  alt={item.titleLine1}
                  style={{
                    position: "absolute",
                    inset: 0,
                    width: "100%",
                    height: "100%",
                    objectFit: "cover",
                    pointerEvents: "none",
                  }}
                />

                {/* Dark Vignette Overlay */}
                <div
                  style={{
                    position: "absolute",
                    inset: 0,
                    background:
                      "linear-gradient(180deg, rgba(0,0,0,0.45) 0%, rgba(0,0,0,0.1) 25%, rgba(0,0,0,0.68) 60%, rgba(0,0,0,0.98) 100%)",
                    pointerEvents: "none",
                    zIndex: 10,
                  }}
                />

                {/* Content Overlay */}
                <div
                  style={{
                    position: "relative",
                    width: "100%",
                    height: "100%",
                    padding: isMobile ? "14px 12px 16px" : "20px 18px 22px",
                    display: "flex",
                    flexDirection: "column",
                    justifyContent: "space-between",
                    textAlign: "center",
                    zIndex: 20,
                    opacity: isCenter ? 1 : 0,
                    transform: isCenter ? "translateY(0px)" : "translateY(16px)",
                    transition: "opacity 450ms ease, transform 450ms ease",
                    pointerEvents: isCenter ? "auto" : "none",
                  }}
                >
                  {/* Tag */}
                  <div style={{ textAlign: "right", width: "100%", paddingRight: "2px" }}>
                    <span
                      style={{
                        display: "inline-block",
                        fontSize: isMobile ? "0.68rem" : "0.74rem",
                        fontWeight: 700,
                        letterSpacing: "0.05em",
                        color: "#ffffff",
                        background: "rgba(0, 0, 0, 0.55)",
                        padding: "3px 9px",
                        borderRadius: "9999px",
                        border: "1px solid rgba(197, 168, 128, 0.45)",
                        textShadow: "0 2px 6px rgba(0,0,0,0.9)",
                      }}
                    >
                      {item.tag}
                    </span>
                  </div>

                  {/* Body Content */}
                  <div
                    style={{
                      display: "flex",
                      flexDirection: "column",
                      alignItems: "center",
                      gap: "3px",
                      marginTop: "auto",
                      paddingBottom: "2px",
                    }}
                  >
                    <h2
                      style={{
                        fontSize: isMobile ? "1.25rem" : "1.55rem",
                        fontWeight: 900,
                        textTransform: "uppercase",
                        letterSpacing: "0.04em",
                        color: "#ffffff",
                        margin: 0,
                        lineHeight: 1.1,
                        textShadow: "0 3px 12px rgba(0,0,0,0.95)",
                      }}
                    >
                      {item.titleLine1}
                    </h2>

                    {item.titleLine2 && (
                      <span
                        style={{
                          fontSize: isMobile ? "0.85rem" : "1.02rem",
                          fontWeight: 700,
                          textTransform: "uppercase",
                          letterSpacing: "0.05em",
                          color: "#f3f0ea",
                          lineHeight: 1.2,
                          textShadow: "0 3px 10px rgba(0,0,0,0.9)",
                        }}
                      >
                        {item.titleLine2}
                      </span>
                    )}

                    <div
                      style={{
                        width: "32px",
                        height: "2px",
                        backgroundColor: "#c5a880",
                        borderRadius: "2px",
                        margin: "4px auto",
                        boxShadow: "0 0 8px rgba(197,168,128,0.7)",
                      }}
                    />

                    {item.desc && (
                      <p
                        style={{
                          fontSize: isMobile ? "0.72rem" : "0.8rem",
                          fontStyle: "italic",
                          color: "rgba(255,255,255,0.92)",
                          maxWidth: isMobile ? "220px" : "270px",
                          margin: "0 0 8px",
                          lineHeight: 1.32,
                          textShadow: "0 2px 8px rgba(0,0,0,0.95)",
                        }}
                      >
                        {item.desc}
                      </p>
                    )}

                    <a
                      href={item.ctaUrl || "#"}
                      onClick={(e) => {
                        if (onCtaClick) {
                          e.preventDefault();
                          onCtaClick(item);
                        }
                      }}
                      style={{
                        display: "inline-flex",
                        alignItems: "center",
                        gap: "6px",
                        padding: isMobile ? "6px 16px" : "7px 20px",
                        borderRadius: "9999px",
                        background: "linear-gradient(135deg, #c5a880 0%, #a48256 100%)",
                        color: "#110d0c",
                        fontSize: isMobile ? "0.68rem" : "0.72rem",
                        fontWeight: 800,
                        letterSpacing: "0.12em",
                        textTransform: "uppercase",
                        textDecoration: "none",
                        boxShadow: "0 4px 14px rgba(0,0,0,0.4), 0 0 15px rgba(197,168,128,0.3)",
                        cursor: "pointer",
                        transition: "transform 200ms ease, box-shadow 200ms ease",
                      }}
                    >
                      <span>{item.ctaText || "Menüyü İncele"}</span>
                      <ArrowRightIcon />
                    </a>
                  </div>
                </div>
              </div>
            );
          })}
        </div>

        {/* Navigation Arrows */}
        <button
          onClick={prevSlide}
          aria-label="Previous dish"
          style={{
            position: "absolute",
            left: isMobile ? "6px" : "16px",
            top: "50%",
            transform: "translateY(-50%)",
            width: isMobile ? "36px" : "44px",
            height: isMobile ? "36px" : "44px",
            borderRadius: "50%",
            backgroundColor: "rgba(0,0,0,0.6)",
            border: "1px solid rgba(255,255,255,0.25)",
            color: "#ffffff",
            display: "flex",
            alignItems: "center",
            justifyContent: "center",
            backdropFilter: "blur(8px)",
            WebkitBackdropFilter: "blur(8px)",
            cursor: "pointer",
            boxShadow: "0 8px 24px rgba(0,0,0,0.5)",
            zIndex: 40,
            transition: "all 250ms ease",
          }}
        >
          <ChevronLeftIcon />
        </button>

        <button
          onClick={nextSlide}
          aria-label="Next dish"
          style={{
            position: "absolute",
            right: isMobile ? "6px" : "16px",
            top: "50%",
            transform: "translateY(-50%)",
            width: isMobile ? "36px" : "44px",
            height: isMobile ? "36px" : "44px",
            borderRadius: "50%",
            backgroundColor: "rgba(0,0,0,0.6)",
            border: "1px solid rgba(255,255,255,0.25)",
            color: "#ffffff",
            display: "flex",
            alignItems: "center",
            justifyContent: "center",
            backdropFilter: "blur(8px)",
            WebkitBackdropFilter: "blur(8px)",
            cursor: "pointer",
            boxShadow: "0 8px 24px rgba(0,0,0,0.5)",
            zIndex: 40,
            transition: "all 250ms ease",
          }}
        >
          <ChevronRightIcon />
        </button>

        {/* Bottom Controller Area */}
        <div className="flex flex-col items-center gap-2 sm:gap-3 w-full z-30">
          {/* Pagination Dots */}
          <div style={{ display: "flex", alignItems: "center", justifyContent: "center", gap: "7px" }}>
            {items.map((_, idx) => (
              <button
                key={idx}
                onClick={() => goToSlide(idx)}
                aria-label={`Go to slide ${idx + 1}`}
                style={{
                  height: "6px",
                  width: idx === currentIndex ? "26px" : "6px",
                  borderRadius: "9999px",
                  backgroundColor: idx === currentIndex ? "#c5a880" : "rgba(255,255,255,0.25)",
                  border: "none",
                  cursor: "pointer",
                  boxShadow: idx === currentIndex ? "0 0 10px rgba(197,168,128,0.8)" : "none",
                  transition: "all 280ms ease",
                  padding: 0,
                }}
              />
            ))}
          </div>

          {/* Quick Category Buttons */}
          <div className="flex gap-2 flex-wrap justify-center max-w-[900px]">
            <a
              href="#0"
              style={{
                display: "inline-flex",
                alignItems: "center",
                gap: "5px",
                padding: "6px 13px",
                borderRadius: "9999px",
                backgroundColor: "rgba(244, 166, 78, 0.12)",
                border: "1px solid rgba(181, 82, 57, 0.4)",
                color: "#f7d5b8",
                fontSize: isMobile ? "0.72rem" : "0.82rem",
                fontWeight: 700,
                textDecoration: "none",
              }}
            >
              🍳 Erkence
            </a>
            <a
              href="#1"
              style={{
                display: "inline-flex",
                alignItems: "center",
                gap: "5px",
                padding: "6px 13px",
                borderRadius: "9999px",
                backgroundColor: "rgba(244, 166, 78, 0.12)",
                border: "1px solid rgba(181, 82, 57, 0.4)",
                color: "#f7d5b8",
                fontSize: isMobile ? "0.72rem" : "0.82rem",
                fontWeight: 700,
                textDecoration: "none",
              }}
            >
              🥪 All Day
            </a>
            <a
              href="#2"
              style={{
                display: "inline-flex",
                alignItems: "center",
                gap: "5px",
                padding: "6px 13px",
                borderRadius: "9999px",
                backgroundColor: "rgba(244, 166, 78, 0.12)",
                border: "1px solid rgba(181, 82, 57, 0.4)",
                color: "#f7d5b8",
                fontSize: isMobile ? "0.72rem" : "0.82rem",
                fontWeight: 700,
                textDecoration: "none",
              }}
            >
              🍰 Tatlılar
            </a>
            <a
              href="#3"
              style={{
                display: "inline-flex",
                alignItems: "center",
                gap: "5px",
                padding: "6px 13px",
                borderRadius: "9999px",
                backgroundColor: "rgba(244, 166, 78, 0.12)",
                border: "1px solid rgba(181, 82, 57, 0.4)",
                color: "#f7d5b8",
                fontSize: isMobile ? "0.72rem" : "0.82rem",
                fontWeight: 700,
                textDecoration: "none",
              }}
            >
              ☕ İçecekler
            </a>
          </div>
        </div>
      </div>
    </section>
  );
}

export const Component = CoverFlowCarousel;
export default CoverFlowCarousel;
