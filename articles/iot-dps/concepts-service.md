---
title: Terminologie používaná s Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Popisuje běžné terminologii používané se službou Device Provisioning (DPS) a IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019442"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>Terminologie IoT Hub Device Provisioning Service (DPS)

IoT Hub Device Provisioning Service je pomocná služba pro IoT Hub, která slouží ke konfiguraci zařízení s nulovým dotykem pro zadané centrum IoT. Služba Device Provisioning umožňuje bezpečným a škálovatelným způsobem [zřizovat](about-iot-dps.md#provisioning-process) miliony zařízení.

Zřizování zařízení je proces dvou částí. První část vytváří počáteční připojení mezi zařízením a řešením IoT tím, že zařízení *registruje* . Druhá část aplikuje správnou *konfiguraci* na zařízení na základě konkrétních požadavků řešení. Po dokončení obou kroků se zařízení kompletně *zřídilo*. Služba Device Provisioning oba kroky automatizuje a zajišťuje tak bezproblémové zřizování zařízení.

Tento článek poskytuje přehled konceptů zřizování, které jsou použitelné pro správu *služby*. Tento článek se nejvíce hodí pro osoby zapojené do [Nastavení cloudu](about-iot-dps.md#cloud-setup-step) , které vám pomůžou při přípravě zařízení na nasazení.

## <a name="service-operations-endpoint"></a>Koncový bod operací služby

Koncový bod operací služby je koncový bod pro správu nastavení služby a údržbu seznamu registrací. Tento koncový bod používá jenom Správce služby. zařízení je nepoužívá.

## <a name="device-provisioning-endpoint"></a>Koncový bod zřizování zařízení

Koncový bod zřizování zařízení je jedním koncovým bodem, který všechna zařízení používají pro Automatické zřizování. Adresa URL je stejná pro všechny instance služby zřizování a eliminuje nutnost opětovného doplňování zařízení s novými informacemi o připojení ve scénářích dodavatelských řetězců. Rozsah ID zajišťuje izolaci tenanta.

## <a name="linked-iot-hubs"></a>Propojená centra IoT

Služba Device Provisioning může zřizovat zařízení jenom pro centra IoT, která jsou s ní propojená. Propojení služby IoT Hub s instancí služby Device Provisioning poskytuje oprávnění ke čtení a zápisu do registru zařízení služby IoT Hub. pomocí tohoto odkazu může služba Device Provisioning zaregistrovat ID zařízení a nastavit počáteční konfiguraci v zařízení s dvojitou vazbou. Propojená centra IoT můžou být v libovolné oblasti Azure. Můžete propojit centra v jiných předplatných s vaší službou zřizování.


## <a name="allocation-policy"></a>Zásady přidělování

Nastavení na úrovni služby, které určuje, jak služba Device Provisioning přiřazuje zařízení do služby IoT Hub. Existují tři podporované zásady přidělování:

* **Rovnoměrně vážená distribuce**: propojená centra IoT mají stejně často zařízení, která jsou jim zřízena. Výchozí nastavení. Pokud zřizujete zařízení pouze v jednom centru IoT, můžete nechat toto nastavení.

* **Nejnižší latence**: zařízení se zřídí do služby IoT Hub s nejnižší latencí pro zařízení. Pokud by více propojených hub IoT poskytovalo stejnou nejnižší latenci, služba zřizování vyhodnotí hodnoty hash zařízení v těchto centrech.

* **Statická konfigurace prostřednictvím seznamu** registrací: specifikace požadovaného centra IoT v seznamu registrací má prioritu pro zásady přidělování na úrovni služby.

* **Vlastní (použití funkce Azure)**: vlastní zásada přidělení vám poskytne lepší kontrolu nad tím, jak se zařízení přiřazují do služby IoT Hub. K tomu je potřeba pomocí vlastního kódu ve službě Azure Function přiřazovat zařízení ke službě IoT Hub. Služba Device Provisioning zavolá váš kód funkce Azure, který poskytuje všechny relevantní informace o zařízení a registraci k vašemu kódu. Váš kód funkce se spustí a vrátí informace o službě IoT Hub použité ke zřízení zařízení.

## <a name="enrollment"></a>Registrace

Registrace je záznam zařízení nebo skupin zařízení, která se můžou registrovat prostřednictvím automatického zřizování. Záznam zápisu obsahuje informace o zařízení nebo skupině zařízení, včetně:
- [mechanismus ověřování](#attestation-mechanism) používaný zařízením
- Volitelná počáteční požadovaná konfigurace
- požadované centrum IoT Hub
- požadované ID zařízení

Služba Device Provisioning podporuje dva typy registrace:

### <a name="enrollment-group"></a>Skupina registrace

Skupina registrace je skupina zařízení, která sdílejí konkrétní mechanismus ověřování. Skupiny registrací podporují rozhraní X. 509 i symetrický. Všechna zařízení ve skupině pro registraci X. 509 obsahují certifikáty X. 509 podepsané stejnou kořenovou nebo zprostředkující certifikační autoritou (CA). Každé zařízení ve skupině pro zápis symetrického klíče prezentuje tokeny SAS odvozené ze skupiny symetrického klíče. Název skupiny registrací a název certifikátu musí být alfanumerické a malými písmeny a můžou obsahovat spojovníky.

> [!TIP]
> Doporučujeme používat skupinu registrací pro velký počet zařízení, která sdílejí požadovanou počáteční konfiguraci, nebo pro všechna zařízení, která se budou napojovat do stejného tenanta.

### <a name="individual-enrollment"></a>Jednotlivé registrace

Jednotlivá registrace je záznam pro jedno zařízení, které se může zaregistrovat. Jednotlivé registrace můžou jako mechanismus ověřování použít buď certifikáty na list X. 509, nebo tokeny SAS (od fyzického nebo virtuálního čipu TPM). ID registrace v individuální registraci je alfanumerické a malé písmeno a může obsahovat spojovníky. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

> [!TIP]
> Pro zařízení, která vyžadují jedinečné počáteční konfigurace, doporučujeme používat jednotlivé registrace nebo pro zařízení, která se dají ověřovat jenom pomocí tokenů SAS prostřednictvím ověření identity čipem TPM.


## <a name="attestation-mechanism"></a>Mechanismus ověřování

Mechanismus ověřování je metoda, která se používá k potvrzení identity zařízení. Mechanismus ověřování je nakonfigurovaný pro zápis a oznamuje službě zřizování, kterou metodu použít při ověřování identity zařízení během registrace.

> [!NOTE]
> IoT Hub používá schéma ověřování pro podobný koncept v dané službě.

Služba Device Provisioning podporuje následující formy ověření identity:
* **Certifikáty x. 509** založené na standardním toku ověřování certifikátu x. 509. Další informace najdete v tématu [ověření identity X. 509](concepts-x509-attestation.md).
* **Trusted Platform Module (TPM)** na základě výzvy s identifikátorem nonce, který používá standard TPM pro klíče k prezentaci podepsaného tokenu sdíleného přístupového podpisu (SAS). To nevyžaduje fyzického čipu TPM v zařízení, ale služba očekává ověření pomocí ověřovacího klíče podle [specifikace čipu TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/). Další informace najdete v tématu [ověření identity čipem TPM](concepts-tpm-attestation.md).
* **Symetrický klíč** založený na [tokenech zabezpečení](../iot-hub/iot-hub-devguide-security.md#security-tokens)sdíleného přístupového podpisu (SAS), které zahrnují signaturu s hodnotou hash a vložené vypršení platnosti. Další informace najdete v tématu [ověření identity symetrického klíče](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Modul hardwarového zabezpečení (HSM) se používá k zabezpečenému hardwarovému úložišti tajných kódů zařízení a jedná se o nejbezpečnější formu tajného úložiště. Certifikáty X. 509 i tokeny SAS můžou být uložené v modulu HSM. HSM je možné použít s mechanismem ověřování, který služba zřizování podporuje.

> [!TIP]
> Důrazně doporučujeme používat modul HARDWAROVÉho zabezpečení a zařízení k bezpečnému ukládání tajných kódů do vašich zařízení.

Tajné kódy zařízení je také možné ukládat v softwaru (paměti), ale jedná se o méně bezpečnou podobu úložiště než modul hardwarového zabezpečení (HSM).



## <a name="id-scope"></a>Rozsah ID

Obor ID se přiřadí službě Device Provisioning, když ji vytvoří uživatel a používá se k jednoznačné identifikaci konkrétní služby zřizování, přes kterou se bude zařízení registrovat. Rozsah ID je vygenerován službou a je neměnný, což zaručuje jedinečnost.

> [!NOTE]
> Jedinečnost je důležitá pro dlouhotrvající operace nasazení a scénáře fúze a akvizice.


## <a name="registration"></a>Registrace

Registrace znamená, že se zařízení úspěšně zaregistrovalo nebo zřídí do IoT Hub prostřednictvím služby Device Provisioning. Registrační záznamy jsou vytvořeny automaticky; je možné je odstranit, ale nelze je aktualizovat.


## <a name="registration-id"></a>ID registrace

ID registrace slouží k jednoznačné identifikaci registrace zařízení ve službě Device Provisioning. ID zařízení musí být v [oboru ID](#id-scope)služby zřizování jedinečné. Každé zařízení musí mít ID registrace. ID registrace je alfanumerické, rozlišuje velká a malá písmena a může obsahovat speciální znaky, včetně dvojtečky, tečky, podtržítka a spojovníku.

* V případě čipu TPM je ID registrace zajištěno samotným čipem TPM.
* V případě ověřování na základě X. 509 se ID registrace poskytuje jako název subjektu certifikátu.

## <a name="device-id"></a>ID zařízení

ID zařízení je ID tak, jak se zobrazuje v IoT Hub. V položce registrace může být nastaveno požadované ID zařízení, není však nutné jej nastavit. Nastavení požadovaného ID zařízení se podporuje jenom v jednotlivých registrech. Pokud v seznamu registrací není požadované ID zařízení, jako ID zařízení se při registraci zařízení použije ID registrace. Přečtěte si další informace o [ID zařízení v IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).



## <a name="operations"></a>Operace

Operace jsou fakturační jednotka služby Device Provisioning. Jedna operace představuje úspěšné dokončení jedné instrukce ke službě. Operace zahrnují registraci a opakovanou registraci zařízení. operace také zahrnují změny na straně služby, jako je přidání položek seznamu registrací a aktualizace položek seznamu registrací.
