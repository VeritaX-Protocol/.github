# VeritaX Protocol

Un motor mínimo y abierto para que **clubes educativos informales** emitan **certificados de asistencia o de cursos de capacitación** que cualquier persona pueda verificar con una URL pública, normalmente un QR impreso en el certificado.

> **Estado:** investigación y laboratorios. El motor de producto **todavía no está construido**. Lo descrito como "planeado" no existe aún, no hay pilotos en producción y no se ha desplegado nada en Mainnet.

---

## 1. Problema y público

Los clubes y comunidades de aprendizaje informal (por ejemplo WayLearn, My First Bitcoin, Satoshi Team, BAF, Cartagena Onchain o Platohedro) entregan constancias de asistencia y de cursos cortos, pero cada una es un PDF o una imagen que nadie puede comprobar fácilmente.

VeritaX busca que ese certificado sea verificable en segundos, sin crear una cuenta y sin llamar al club.

Los primeros pilotos previstos son **Cartagena Onchain** y **Platohedro**, organizaciones cercanas al proyecto. Son planes: aún no hay ninguno en marcha.

**Alcance.** VeritaX no emite títulos académicos ni certificados con responsabilidad legal mayor. Por ahora solo certificados de asistencia y de curso de capacitación.

## 2. Qué se construye y por qué

VeritaX construye su **propio motor mínimo**, que corre sobre **Stellar (Soroban)**, y ese motor es el que se usa para emitir las certificaciones de los clubes. No es solo una capa de attestations: incluye esquemas, emisores autorizados, registro, revocación y verificación pública.

Existen servicios de attestations sobre Stellar y otras cadenas. VeritaX construye un motor mínimo enfocado en clubes educativos informales y lo mantiene abierto para interoperar con otros servicios más adelante. Algunas de esas ideas, como las de ACTA, se consideran para etapas futuras y otros usos.

## 3. Cómo funciona (visión general)

1. **Esquema:** un archivo JSON que describe los campos de un certificado. Los esquemas tienen versiones: una versión nueva enlaza a la anterior y lo ya emitido sigue siendo válido.
2. **Emisor autorizado:** cada esquema define qué clubes pueden emitir con él. Un club puede estar activo o retirado; si se retira, **sus certificados emitidos se conservan** (retirarse no es revocar).
3. **Registro:** al emitir, el contrato guarda un registro básico con topes por campo, por número de campos y por tamaño (sin textos libres). Los valores concretos se miden en testnet.
4. **Revocación y estado:** el club puede revocar un certificado, y su estado se puede consultar.
5. **Verificación pública:** cualquiera consulta el certificado desde la URL del QR, sin cuenta, y ve título, entidad emisora y demás datos.
6. **Respaldo:** cada certificado se puede exportar como **JSON firmado**, para no depender de ninguna cadena. El motor es la fuente de verdad; el JSON es el formato de intercambio y respaldo.

Visto de punta a punta (**planeado**; el motor aún no existe):

```
 Club o entidad                    Motor VeritaX (Stellar)             Quien verifica
 ──────────────                    ──────────────────────             ──────────────
 define el esquema   ──►  guarda el esquema (versionado)
 y emite el
 certificado         ──►  registra el certificado en el
                          contrato (datos mínimos)
 entrega el PDF/QR ─────────────────────────────────────────►  escanea el QR y abre la
 a la persona                                                  URL pública: ve título,
                                                               emisor, fecha y estado
 (puede revocar)     ──►  marca el registro como revocado ──►  la verificación lo muestra
```

Datos de la versión 1: nombre completo, sin documento de identidad. Hay dos campos opcionales reservados y vacíos (referencia a datos cifrados e identificador del miembro).

## 4. Mapa de repositorios

La organización existe para no mezclar proyectos: cada repositorio tiene un propósito único.

| Repositorio | Qué es | Estado |
|---|---|---|
| [`.github`](https://github.com/VeritaX-Protocol/.github) | Este README de la organización | Activo |
| [`eas-lab`](https://github.com/VeritaX-Protocol/eas-lab) | **Laboratorio conceptual** para entender cómo funciona EAS (Ethereum Attestation Service) en Base y qué sigue vigente. No es código de producto | En curso: ejercicios 01 a 03 publicados |
| [`activities`](https://github.com/VeritaX-Protocol/activities) | Repositorio auxiliar que replica los commits de los demás para que [Stellar Apex](https://stellarapex.org) los registre | Activo |
| Repos de producto | Motor mínimo sobre Stellar (contrato Soroban, bibliotecas internas, etc.) | Por venir |

## 5. Beneficios (esperados)

- **Verificación inmediata y pública:** escaneando un QR, sin crear una cuenta ni escribir al club.
- **Difícil de falsificar:** el registro vive en la cadena y no se puede alterar a escondidas.
- **Revocación:** el club puede invalidar un certificado y la verificación lo muestra.
- **Independiente del emisor:** el registro sigue verificable aunque el club cambie de sistema o se retire.
- **Trazabilidad:** quién emitió, cuándo y con qué versión del esquema.
- **Respaldo portable:** el JSON firmado se puede exportar y conservar fuera de la cadena.
- **Costos por medir:** el costo real por certificado se mide en testnet; este README no fija precios.

## 6. Hoja de ruta por etapas

Sin fechas rígidas; cada etapa depende de que la anterior salga bien.

- **v1 (motor mínimo):** subir esquemas con versionado, emisores autorizados por esquema, registro básico en el contrato, revocación, consulta de estado, verificación pública por URL/QR y respaldo en JSON firmado.
- **v1.1:** panel para organizadores, constructor de esquemas sin código y emisión por lotes con raíz Merkle, solo si el costo medido lo exige.
- **v2:** datos sensibles cifrados en una base de datos (nunca en la cadena), cuentas de miembro y exportación a credencial W3C 2.0.
- **v3:** SDK para terceros. Por ahora solo hay bibliotecas de uso interno.

## 7. Principios

- **Datos mínimos:** solo lo necesario para verificar.
- **Nada sensible en la cadena.** Lo sensible, cuando exista, irá cifrado fuera de la cadena en etapas futuras.
- **Respaldo propio:** el JSON firmado permite no depender de una sola cadena.
- **Sin proveedor único.**
- **Código abierto.**

## 8. Cadenas

- **Hoy: Stellar.** Todo el trabajo del motor se hace sobre Stellar.
- **Futuro:** Base (que incluye EAS de forma nativa), Solana y BNB podrían añadirse mediante un adaptador sobre el mismo núcleo, y solo con una demanda por escrito.

## 9. Alcance y límites (importante)

VeritaX es un **testigo técnico**. Puede respaldar, hasta cierto punto:

- que el registro **existe** y no se alteró desde que se creó;
- que lo emitió una **entidad autorizada** para ese esquema;
- **cuándo** se registró;
- si fue **revocado** o no.

Lo que **no** hace:

- **No certifica la validez académica ni legal** de lo que dice el certificado. Esa responsabilidad es del club que lo emite.
- **No es firma digital** ni **estampado cronológico** de una entidad de certificación digital acreditada.
- "**No encontrado**" **no significa "falso"**: puede ser un error de lectura, otra cadena o un certificado emitido fuera del sistema. La verificación debe distinguir estados y mostrar avisos aclaratorios.

## 10. Lo explorado en el laboratorio

Antes de decidir el diseño se está explorando EAS en la testnet de Base (Base Sepolia) en [`eas-lab`](https://github.com/VeritaX-Protocol/eas-lab): esquemas, attestations, lectura y verificación, y más adelante revocación, resolvers y modo fuera de la cadena. Es **exploración para tener criterio**; sus decisiones y su código no son el diseño del producto.

## 11. Cómo seguir el proyecto

El avance se sigue en [Stellar Apex](https://stellarapex.org), que registra commits y actualizaciones semanales. El repositorio [`activities`](https://github.com/VeritaX-Protocol/activities) reúne los commits de toda la organización para ese fin.
