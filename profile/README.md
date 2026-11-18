# VeritaX Protocol

Un **sistema para crear certificados** pensado para **entidades educativas informales**: constancias de asistencia, de participación en un evento, de cursos de capacitación y otros documentos **no legales** que la entidad necesite. Cualquier persona puede verificarlos con una URL pública, normalmente un QR impreso en el documento. Por dentro, el sistema usa un **motor propio**.

> **Estado:** investigación y laboratorios. El sistema y su motor **todavía no están construidos**. Lo descrito como "planeado" no existe aún, no hay pilotos en producción y no se ha desplegado nada en Mainnet.

---

## 1. Problema y público

Los clubes y comunidades de aprendizaje informal (por ejemplo WayLearn, My First Bitcoin, Satoshi Team, BAF, Cartagena Onchain o Platohedro) entregan constancias de asistencia, de participación en eventos y de cursos cortos, pero cada una es un PDF o una imagen que nadie puede comprobar fácilmente.

VeritaX busca que ese documento sea verificable en segundos, sin crear una cuenta y sin llamar al club.

Los primeros pilotos previstos son **Cartagena Onchain** y **Platohedro**, organizaciones cercanas al proyecto. Son planes: aún no hay ninguno en marcha.

**Alcance.** Por ahora VeritaX solo crea documentos **no legales**: asistencia, participación en un evento, curso de capacitación y otros que una entidad educativa informal requiera. No emite títulos académicos ni certificados con responsabilidad legal.

## 2. Qué se construye y por qué

**VeritaX** es la plataforma con la que una entidad educativa informal define el tipo de documento que necesita, lo emite, lo anula si hace falta y permite que cualquiera lo verifique.

**Dentro del sistema se usa un motor propio:** un componente mínimo que corre sobre **Stellar (Soroban)**, con **un contrato por entidad**, y se encarga de los esquemas, los emisores autorizados, el registro, la anulación y la verificación. El motor es una pieza interna del sistema; lo que ven los clubes y quienes verifican es el sistema completo.

**El motor es de implementación propia.** Existen servicios de attestations sobre Stellar y otras cadenas (EAS, Attest Protocol y otros); VeritaX los estudia como referencia conceptual, pero no hereda su código ni su estructura de datos. El motor queda abierto para interoperar con otros servicios más adelante. Algunas ideas, como las de ACTA, se consideran para etapas futuras y otros usos.

## 3. Cómo funciona (visión general)

1. **Esquema:** un archivo JSON que describe el tipo de certificado (título o curso). Los esquemas tienen versiones: una versión nueva enlaza a la anterior y lo ya emitido sigue siendo válido.
2. **Emisor autorizado:** cada entidad tiene su contrato y autoriza a sus emisores. Un emisor puede estar activo o retirado; si se retira, **sus certificados emitidos se conservan** (retirarse no es anular), pero **ya no puede anular**.
3. **Registro:** al emitir, el contrato guarda en la cadena **todo lo no personal**: entidad, título, emisor, fechas, estado y **dos huellas con sal** (una del nombre y otra del documento de identidad). **El nombre y el documento nunca van en la cadena:** quedan cifrados en una base de datos, junto con su sal. Hay topes de tamaño y sin textos libres; los valores concretos se miden en testnet.
4. **Anulación:** no hay ediciones. Solo el emisor autorizado puede **anular** un certificado y, si hace falta, **emitir uno nuevo** en su lugar. Un certificado anulado pierde su validez y la vista pública muestra solo un banner **«ANULADO»** y la entidad emisora.
5. **Verificación pública en tres niveles:**
   - **Público (QR):** título, entidad, fechas y estado, **sin datos personales**.
   - **Enlace de lectura:** el titular puede generar, desde su panel, enlaces con **fecha de caducidad** que además muestran el nombre. Puede revocarlos.
   - **Contraste de identidad:** quien ya tiene el documento de la persona por otro medio lo escribe y el sistema responde "coincide" o "no coincide". **El documento nunca se muestra** y se verifica dentro del servidor.
6. **Credencial exportable (opcional):** el certificado se puede exportar como **credencial verificable W3C (VC)** compatible con **Open Badges 3.0**, para no depender del sistema. No es obligatoria: con las credenciales de la entidad o del titular se verifica sin ella.

Visto de punta a punta (**planeado**; el motor aún no existe):

```
 Club o entidad                    Motor VeritaX (Stellar)             Quien verifica
 ──────────────                    ──────────────────────             ──────────────
 define el esquema   ──►  guarda el esquema (versionado)
 y emite el
 certificado         ──►  registra el certificado en el contrato
                          (datos no personales + huellas con sal)
                          y guarda los datos personales cifrados
                          en la base de datos
 entrega el PDF/QR ─────────────────────────────────────────►  escanea el QR y abre la
 a la persona                                                  URL pública: ve título,
                                                               entidad, fecha y estado
 (puede anular)      ──►  marca el registro como anulado ──►   la verificación muestra
                                                               "ANULADO" y la entidad
```

## 4. Mapa de repositorios

La organización existe para no mezclar proyectos: cada repositorio tiene un propósito único.

| Repositorio | Qué es | Estado |
|---|---|---|
| [`.github`](https://github.com/VeritaX-Protocol/.github) | Este README de la organización | Activo |
| [`eas-lab`](https://github.com/VeritaX-Protocol/eas-lab) | **Laboratorio conceptual** para entender cómo funciona EAS (Ethereum Attestation Service) en Base y qué sigue vigente. No es código de producto | En curso: ejercicios 01 a 04 publicados |
| [`sas-lab`](https://github.com/VeritaX-Protocol/sas-lab) | **Laboratorio conceptual** para entender cómo funciona Attest Protocol en la testnet de Stellar (Soroban): almacenamiento, costos, autorización y actualización de contratos. No es código de producto | En curso |
| [`ario-lab`](https://github.com/VeritaX-Protocol/ario-lab) | **Laboratorio conceptual** para entender cómo guardar plantillas de certificado (SVG) en Arweave con el servicio de subida de ar.io (antes ArDrive Turbo): costos por tamaño, formato del id, lectura por gateways y render seguro. No es código de producto | Ejercicios planteados, aún sin ejecutar |
| [`activities`](https://github.com/VeritaX-Protocol/activities) | Repositorio auxiliar que replica los commits de los demás para que [Stellar Apex](https://stellarapex.org) los registre | Activo |
| Repos de producto | El sistema de certificados y su motor propio sobre Stellar (contrato Soroban, bibliotecas internas, etc.) | Por venir |

## 5. Beneficios (esperados)

- **Verificación inmediata y pública:** escaneando un QR, sin crear una cuenta ni escribir al club.
- **Difícil de falsificar:** el registro vive en la cadena y no se puede alterar a escondidas.
- **Privacidad por diseño:** nombre y documento no están en la cadena; el titular decide quién ve su nombre y por cuánto tiempo.
- **Anulación visible:** si el club anula un certificado, la verificación lo muestra como anulado.
- **Independiente del emisor:** el registro sigue verificable aunque el club cambie de sistema o se retire.
- **Trazabilidad:** quién emitió, cuándo y con qué versión del esquema.
- **Estándares abiertos:** exportación opcional a credencial W3C VC y Open Badges 3.0.
- **Costos por medir:** el costo real por certificado se mide en testnet; este README no fija precios.

## 6. Hoja de ruta por etapas

Sin fechas rígidas; cada etapa depende de que la anterior salga bien.

- **v1 (sistema con motor mínimo):** esquemas con versionado, un contrato por entidad, emisores activos o retirados, registro con huellas con sal, anulación con reemplazo y verificación pública por URL/QR. En desarrollo y testnet las huellas llevan solo sal y datos ficticios.
- **v1.1:** paneles de la entidad y del titular (enlaces de lectura con caducidad, contraste de identidad), **exportación a credencial W3C VC / Open Badges 3.0 para certificados**, constructor de esquemas sin código y emisión por lotes, solo si el costo medido lo exige.
- **Antes de entidades reales:** gestor de llaves (KMS) para las llaves de emisión y para proteger las huellas del documento. No se usa en desarrollo.
- **v2:** insignias (Open Badges), cuentas de miembro.
- **v3:** SDK para terceros. Por ahora solo hay bibliotecas de uso interno.

## 7. Principios

- **Datos mínimos:** solo lo necesario para verificar.
- **Nada personal en la cadena:** ni nombre ni documento; solo huellas con sal. Lo personal va cifrado fuera de la cadena.
- **El titular manda:** nadie ve su nombre sin un enlace suyo con caducidad.
- **Sin ediciones:** se anula y se emite uno nuevo.
- **Estándares abiertos:** W3C VC y Open Badges.
- **Sin proveedor único.**
- **Código abierto.**

## 8. Cadenas

- **Hoy: Stellar.** Todo el trabajo del motor se hace sobre Stellar.
- **Futuro:** Base (que incluye EAS de forma nativa), Solana y BNB podrían añadirse mediante un adaptador sobre el mismo núcleo, y solo con una demanda por escrito.

## 9. Alcance y límites (importante)

VeritaX es un **testigo técnico**. Puede respaldar, hasta cierto punto:

- que el registro **existe** y no se alteró desde que se creó;
- que lo emitió una **entidad autorizada**;
- **cuándo** se registró;
- si fue **anulado** o no.

Lo que **no** hace:

- **No certifica la validez académica ni legal** de lo que dice el certificado. Esa responsabilidad es del club que lo emite.
- **No es firma digital** ni **estampado cronológico** de una entidad de certificación digital acreditada. La firma criptográfica de la entidad sobre una credencial exportada es otra cosa y no tiene valor legal por sí sola.
- "**No encontrado**" **no significa "falso"**: puede ser un error de lectura, otra cadena o un certificado emitido fuera del sistema. La verificación debe distinguir estados y mostrar avisos aclaratorios.
- **Permanencia:** en Soroban nada vive para siempre sin renovación. Mantener los registros vigentes tiene un costo recurrente que se medirá y que debe asumirse.
- **Lo que ya se vio no se "des-ve":** un enlace de lectura con caducidad impide accesos futuros, no una captura que alguien haya tomado antes.
- **La verificación del documento depende de VeritaX:** se hace dentro del servidor, no por cuenta propia de un tercero.

## 10. Lo explorado en el laboratorio

Antes de decidir el diseño se explora EAS en la testnet de Base (Base Sepolia) en [`eas-lab`](https://github.com/VeritaX-Protocol/eas-lab) y Attest Protocol en la testnet de Stellar en [`sas-lab`](https://github.com/VeritaX-Protocol/sas-lab): esquemas, attestations, lectura y verificación, revocación, almacenamiento, costos y actualización de contratos. Además, [`ario-lab`](https://github.com/VeritaX-Protocol/ario-lab) plantea cómo guardar en Arweave las plantillas gráficas de los certificados. Es **exploración para tener criterio**; sus decisiones y su código no son el diseño del producto.

## 11. Cómo seguir el proyecto

El avance se sigue en [Stellar Apex](https://stellarapex.org), que registra commits y actualizaciones semanales. El repositorio [`activities`](https://github.com/VeritaX-Protocol/activities) reúne los commits de toda la organización para ese fin. 
