# Bandada U — kit de marca

Concepto: nueve aves en vuelo forman una **U**; la última (arriba a la derecha) es la que guía el grupo y lleva el amarillo de la paraba. La misma geometría genera los SVG, los PNG y el componente React Native.

| Color | Hex |
|---|---|
| Azul río | `#1238A8` |
| Azul noche | `#0A1F66` |
| Amarillo paraba | `#FFC21A` (sobre fondo claro: ámbar `#F5A100`) |

Tipografía del wordmark: Nunito ExtraBold, convertida a trazos (no hay que instalar fuentes).

## Contenido
- `svg/` marca (`color`, `light`, `mono`), lockups horizontal y apilado, marca compacta de 5 aves, `favicon.svg`, y dos animados (`mark-loader.svg`, `splash-animated.svg`; respetan modo oscuro y `prefers-reduced-motion`).
- `app-icons/` PNG listos para Expo (ver abajo).
- `react-native/BandadaLogo.tsx` → `BandadaMark`, `BandadaWordmark`, `BandadaLoader`, `BandadaSplash`.

## Instalación (Expo)
```bash
mkdir -p assets/brand && cp app-icons/* assets/brand/
cp react-native/BandadaLogo.tsx src/components/
npx expo install react-native-svg react-native-reanimated expo-splash-screen
```

## app.config.js
```js
icon: './assets/brand/icon-1024.png',            // string en la raíz
ios: {
  icon: './assets/brand/icon-1024.png',
  // iOS 18+ (opcional): icon: { light: '...icon-1024.png', dark: '...icon-1024-dark.png', tinted: '...icon-1024-tinted.png' }
},
android: {
  adaptiveIcon: {
    foregroundImage: './assets/brand/android-adaptive-foreground.png',
    monochromeImage: './assets/brand/android-adaptive-monochrome.png', // Android 13+ (iconos temáticos)
    backgroundColor: '#1238A8',
  },
},
web: { favicon: './assets/brand/favicon.png' },
plugins: [
  ['expo-splash-screen', { image: './assets/brand/splash-icon.png', imageWidth: 200, resizeMode: 'contain', backgroundColor: '#0A1F66' }],
  ['expo-notifications', { icon: './assets/brand/notification-icon.png', color: '#1238A8' }],
],
```

## Splash animada sin parpadeo
El splash nativo muestra la marca armada (200 px, centrada). `BandadaSplash` usa el mismo tamaño y centro, así que continúa la animación desde ese punto.

```tsx
import * as SplashScreen from 'expo-splash-screen';
import { BandadaSplash } from './components/BandadaLogo';

SplashScreen.preventAutoHideAsync();

export default function Root() {
  const [done, setDone] = useState(false);
  return (
    <>
      <App />
      {!done && <BandadaSplash onLayout={() => SplashScreen.hideAsync()} onFinish={() => setDone(true)} />}
    </>
  );
}
```
- `intro="scattered"`: las aves llegan volando (úsalo si prefieres que el splash nativo no muestre la marca).
- Loader: `<BandadaLoader size={56} />`; en botones, `size={24}` con `color="#fff"`.
- Marca en headers: `<BandadaMark size={32} />`.

## Qué se verificó y qué no
Verificado aquí: lienzo 1024 sin transparencia en iOS (`icon-1024.png`, sin esquinas redondeadas: iOS las aplica), radio máximo del foreground adaptativo 288 px frente a 313 px de zona segura de Android (66 dp de 108 dp), `notification-icon.png` solo blanco con alfa (requisito de Android), vista previa con máscaras circular, squircle y temática, y que el `.tsx` compila.
**Pendiente de probar en dispositivo:** las animaciones de React Native, los íconos `dark`/`tinted` de iOS 18 y el ícono temático de Android 13+.

## Regenerar
La geometría vive en `sample_u()` (`gen.py`); si cambias el número de aves o el radio, regenera SVG, PNG y componente juntos para que no se desalineen.
