# VeritaX Protocol

Sistema para **emitir y verificar certificados, diplomas, carnets y constancias de eventos** con respaldo técnico en una blockchain, sin exponer datos personales.

> **Estado:** fase de aprendizaje y pruebas (spikes). Solo testnet y llaves de prueba. Nada de lo descrito aquí como "planificado" está implementado todavía.

---

## 1. Qué es

VeritaX permite que una **entidad** (universidad, instituto, academia, organizador de eventos) emita documentos verificables a sus estudiantes o asistentes, y que cualquier persona pueda comprobar después, en segundos, que ese documento existe, quién lo emitió y que no fue alterado.

Tipos de documento:

- Títulos y diplomas
- Certificaciones y constancias de cursos
- Carnets (estudiantiles, de membresía, etc.)
- Constancias de asistencia a eventos

Cada cliente es una entidad independiente: emite sus propios documentos, asocia una **plantilla** a cada curso, certificación o evento, y puede **revocar** cualquier documento que haya emitido.

## 2. Cómo funciona

```
 Entidad                          VeritaX                           Quien verifica
 ───────                          ───────                           ──────────────
 prepara y aprueba   ──►  cifra los datos personales
 el certificado           (base de datos propia)
                          calcula hash con sal      ──►  registro (attestation)
                          genera PDF con QR              en Base con EAS:
                                                         solo datos no identificables
                                                         + hash con sal
 entrega PDF/QR  ─────────────────────────────────────►  escanea el QR, recalcula
 al titular                                              el hash y compara con la cadena
```

1. **Plantilla:** un diseño SVG genérico, sin datos personales, guardado de forma permanente en Arweave.
2. **Datos personales** (nombre, documento): viven **cifrados** en una base de datos propia. Nunca en la cadena ni en Arweave.
3. **Registro:** se crea una *attestation* de EAS (Ethereum Attestation Service) en Base. Contiene solo datos no identificables y un **hash con sal** de los datos personales.
4. **Verificación:** se recalcula el hash con los datos y la sal del documento y se compara con el de la cadena. Además se revisa que no esté revocado y que el emisor sea una entidad registrada.

## 3. Por qué Base (una cadena basada en Ethereum con EAS nativo)

- **EAS ya viene incluido.** En Base, y en las demás cadenas del stack OP, EAS y su registro de schemas son contratos predesplegados. No hay que desplegar ni mantener el protocolo base: se usa directamente.
- **Seguridad heredada de Ethereum.** Base es una L2 que publica sus datos en Ethereum, por lo que un registro no se puede alterar a escondidas.
- **Costos bajos por certificado**, adecuados para emitir en volumen. Las cifras reales se miden en el ejercicio de costos; este README no fija precios.
- **Ecosistema maduro:** herramientas estándar (ethers, SDK de EAS, Hardhat) y amplia documentación.
- **Cadena configurable.** El `chainId`, el RPC y las direcciones de los contratos vienen de configuración, nunca fijos en el código. Si más adelante conviene otra cadena con EAS, el cambio es de configuración.

Los ejercicios actuales usan **Base Sepolia** (testnet). Aún no se usa mainnet.

## 4. Pagos (planificado)

El cobro del servicio a las entidades se resolverá con una capa de pagos separada de los certificados. Los certificados siempre se registran en Base; los pagos son otro tema.

- **Stellar** se habilitará como pasarela de pagos.
- **Medios de pago tradicionales** a través de pasarelas bancarias (por ejemplo, tarjeta y transferencia).
- **Rampas cripto** (de entrada y de salida de dinero) para quien prefiera pagar con criptomonedas.

Esta parte está por diseñar: aún no hay proveedores definidos ni fechas.

## 5. Beneficios

- **Verificación inmediata y pública:** escaneando un QR, sin llamar ni escribir a la entidad.
- **Resistente a la falsificación:** cualquier cambio en los datos produce un hash distinto al registrado.
- **Revocación:** la entidad puede invalidar un documento y la verificación lo mostrará.
- **Independiente del emisor:** el registro sigue verificable aunque la entidad cambie de sistema.
- **Trazabilidad:** quién emitió, cuándo y con qué plantilla.
- **Plantillas permanentes y versionadas** (Arweave).
- **Bajo costo operativo** frente a procesos manuales de validación.

## 6. Privacidad: no se exponen datos sensibles

- **Ningún dato personal en la cadena ni en Arweave.** Jamás.
- **Hash con sal:** cada certificado lleva una sal aleatoria única. Nunca se publica algo como `hash(cédula)` a secas, que se podría adivinar probando números.
- **Datos cifrados:** los datos personales se guardan cifrados (AES-GCM) en la base de datos de la entidad. Las llaves no viven en el código ni en el repositorio.
- **Supresión posible:** si una persona pide eliminar sus datos, se destruyen los datos y la sal; el hash que queda en la cadena ya no se puede asociar a nadie.
- **Quien tiene el documento decide a quién se lo muestra:** la verificación necesita los datos y la sal que lleva el propio documento.

## 7. Alcance y límites (importante)

VeritaX es un **testigo técnico**. Lo que puede respaldar, hasta cierto punto:

- que el registro **existe** y no se alteró desde que se creó;
- que lo emitió una **entidad registrada** como autorizada;
- **cuándo** se registró;
- que los datos presentados **coinciden** con los registrados;
- si fue **revocado** o no.

Lo que **no** hace:

- **No certifica la validez académica ni legal** del título o certificado. Esa validez la da únicamente la entidad que lo emite (universidad, instituto, organizador, etc.).
- **No es firma digital** ni **estampado cronológico** de una entidad de certificación digital acreditada.
- "**No encontrado**" **no significa "falso"**: puede ser un error de lectura, otra cadena o un documento emitido fuera del sistema. La verificación distingue estados y muestra avisos aclaratorios.

## 8. Estado del proyecto

Se está construyendo por etapas con ejercicios cortos y desechables, antes de escribir las especificaciones del producto.

- Laboratorio EAS: [`eas-lab`](https://github.com/VeritaX-Protocol/eas-lab) (tabla de contenido y reglas en [`eas-lab/LEEME.md`](https://github.com/VeritaX-Protocol/eas-lab/blob/main/LEEME.md))
- Resultados de cada ejercicio: `eas-lab/resultados/NN-nombre/` (cada uno es un proyecto autocontenido)
- Avance: ningún ejercicio terminado todavía. El ejercicio 01 (conexión a Base Sepolia, wallets de prueba y verificación de EAS) está pendiente.
- Reglas del proceso: solo testnet, llaves de prueba, el código de los spikes no pasa a producción sin una especificación.

## 9. Stack técnico

| Pieza | Tecnología |
|---|---|
| Cadena y attestations | Base (Sepolia en pruebas) + EAS |
| Emisores autorizados | Contrato *resolver* propio, con Hardhat 3 |
| Plantillas | SVG en Arweave |
| Datos personales | Base de datos propia, cifrada |
| Código | TypeScript, ethers v6, SDK de EAS |
| Pagos | Stellar, pasarelas bancarias y rampas cripto (planificado) |
