---
title: Zabezpečení a ověřování – Azure Event Grid IoT Edge | Microsoft Docs
description: Zabezpečení a ověřování v Event Grid IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 42f6107ff79d6262cdc0a35cf972cf65d3a9a802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171376"
---
# <a name="security-and-authentication"></a>Zabezpečení a ověřování

Zabezpečení a ověřování je pokročilý koncept a vyžaduje nejprve znalosti Event Grid základy. Začněte [tady](concepts.md) , pokud Event Grid IoT Edge. Event Grid modul vytváří na stávající infrastruktuře zabezpečení na IoT Edge. Podrobnosti a nastavení najdete v [této dokumentaci](../../iot-edge/security.md) .

Následující části podrobně popisují, jak jsou tato nastavení zabezpečená a ověřená:

* Konfigurace TLS
* Příchozí ověřování klientů
* Ověřování odchozího serveru
* Odchozí ověřování klientů

>[!IMPORTANT]
>Zabezpečení a ověřování modulů Event Grid využívá stávající infrastrukturu dostupnou v IoT Edge. Předpokladem je, že IoT Edge dílčí systém je zabezpečený.

>[!IMPORTANT]
>Konfigurace Event Grid je **ve výchozím nastavení zabezpečená**. Následující pododdíly vysvětlují všechny možnosti a možné hodnoty, které můžete použít k přepsání aspektů ověřování. Před provedením změn si Pochopte dopad. Aby se změny projevily, musí se modul Event Grid znovu nasadit.

## <a name="tls-configuration-aka-server-authentication"></a>Konfigurace TLS (a. k. ověřování serveru)

Event Grid modul hostuje koncové body HTTP i HTTPS. Každému IoT Edge modulu je přiřazen certifikát serveru pomocí procesu démon zabezpečení IoT Edge. K zabezpečení koncového bodu používáme certifikát serveru. Po vypršení platnosti se modul automaticky aktualizuje pomocí nového certifikátu z procesu démona zabezpečení IoT Edge.

Ve výchozím nastavení je povolená jenom komunikace HTTPS. Toto chování můžete přepsat pomocí  **inbound__serverAuth__tlsPolicy** konfigurace. Následující tabulka zachycuje možné hodnoty této vlastnosti.

| Možné hodnoty | Popis |
| ---------------- | ------------ |
| Striktní | Default (Výchozí). Povoluje pouze HTTPS
| Povoleno | Povoluje protokol HTTP i HTTPS
| Zakázáno | Povoluje pouze protokol HTTP

## <a name="inbound-client-authentication"></a>Příchozí ověřování klientů

Klienti jsou entity prováděné operacemi správy a/nebo za běhu. Klienty můžou být jiné IoT Edge moduly, jiné aplikace než IoT.

Event Grid modul podporuje dva typy ověřování klientů:

* Založený na klíči SAS (Shared Access Signature)
* na základě certifikátu

Ve výchozím nastavení je modul Event Grid nakonfigurovaný tak, aby přijímal jenom ověřování na základě certifikátu. Při spuštění Event Grid modul načte "TrustBundle" z IoT Edge démon zabezpečení a použije ho k ověření jakéhokoli klientského certifikátu. Klientské certifikáty, které neřeší tento řetěz, budou odmítnuty `UnAuthorized` .

### <a name="certificate-based-client-authentication"></a>Ověřování klientů na základě certifikátu

Ověřování na základě certifikátu je ve výchozím nastavení zapnuté. Ověřování na základě certifikátů můžete zakázat prostřednictvím **inbound__clientAuth__clientCert__enabled** vlastností. Následující tabulka zachycuje možné hodnoty.

| Možné hodnoty | Popis |
| ----------------  | ------------ |
| true | Default (Výchozí). Vyžaduje, aby všechny požadavky na modul Event Grid mohl předložit klientský certifikát. Kromě toho budete muset nakonfigurovat **inbound__clientAuth__clientCert__source**.
| false (nepravda) | Vynutí, aby klient prezentující certifikát.

Následující tabulka zachycuje možné hodnoty pro **inbound__clientAuth__clientCert__source**

| Možné hodnoty | Popis |
| ---------------- | ------------ |
| IoT Edge | Default (Výchozí). K ověření všech klientských certifikátů používá Trustbundle IoT Edge.

Pokud klient zobrazí podepsané svým držitelem, ve výchozím nastavení Event Grid modul tyto žádosti odmítne. Můžete zvolit, aby klientské certifikáty podepsané svým držitelem povolili prostřednictvím vlastnosti **inbound__clientAuth__clientCert__allowUnknownCA** . Následující tabulka zachycuje možné hodnoty.

| Možné hodnoty | Popis |
| ----------------  | ------------|
| true | Default (Výchozí). Umožňuje úspěšné předložení certifikátů podepsaných svým držitelem.
| false (nepravda) | Selže, pokud se zobrazí certifikáty podepsané svým držitelem.

>[!IMPORTANT]
>V produkčních scénářích možná budete chtít nastavit **inbound__clientAuth__clientCert__allowUnknownCA** na **hodnotu NEPRAVDA**.

### <a name="sas-key-based-client-authentication"></a>Ověřování klienta na základě klíčů SAS

Kromě ověřování založeného na certifikátech může modul Event Grid také provádět ověřování na základě klíčů SAS. Klíč SAS je jako tajný kód nakonfigurovaný v modulu Event Grid, který by měl použít k ověření všech příchozích volání. Klienti musí zadat tajný klíč v hlavičce HTTP AEG-SAS-Key. Požadavek se odmítne, `UnAuthorized` Pokud se neshoduje.

Konfigurace pro řízení ověřování založeného na klíčích SAS je **inbound__clientAuth__sasKeys__enabled**.

| Možné hodnoty | Popis  |
| ----------------  | ------------ |
| true | Povoluje ověřování založené na klíčích SAS. Vyžaduje **inbound__clientAuth__sasKeys__key1** nebo **inbound__clientAuth__sasKeys__key2**
| false (nepravda) | Default (Výchozí). Ověřování na základě klíčů SAS je zakázané.

 **inbound__clientAuth__sasKeys__key1** a **inbound__clientAuth__sasKeys__key2** jsou klíče, které konfigurujete modul Event Grid, aby kontroloval příchozí požadavky. Je nutné nakonfigurovat alespoň jeden z klíčů. Klient, který požadavek vydává, bude muset klíč prezentovat jako součást hlavičky žádosti "**AEG-SAS-Key**". Pokud jsou nakonfigurovány oba klíče, může klient buď zobrazit jeden z klíčů.

> [!NOTE]
>Můžete nakonfigurovat obě metody ověřování. V takovém případě se klíč SAS kontroluje jako první a jenom v případě, že se to nepovede, provádí se ověřování na základě certifikátu. Aby požadavek bylo úspěšné, musí být úspěšné jenom jedna z metod ověřování.

## <a name="outbound-client-authentication"></a>Odchozí ověřování klientů

Klient v odchozím kontextu odkazuje na modul Event Grid. Prováděná operace doručuje události předplatitelům. Moduly přihlašování se považují za server.

Každému IoT Edge modulu se přiřadí certifikát identity démon zabezpečení IoT Edge. Pro odchozí hovory používáme certifikát identity. Po vypršení platnosti se modul automaticky aktualizuje pomocí nového certifikátu z procesu démona zabezpečení IoT Edge.

Konfigurace pro řízení odchozího ověřování klientů je **outbound__clientAuth__clientCert__enabled**.

| Možné hodnoty | Popis |
| ----------------  | ------------ |
| true | Default (Výchozí). Vyžaduje, aby všechny odchozí požadavky z modulu Event Grid obsahovaly certifikát. Je nutné nakonfigurovat **outbound__clientAuth__clientCert__source**.
| false (nepravda) | Nevyžadovat, aby modul Event Grid k dispozici certifikát.

Konfigurace, která řídí zdroj certifikátu, je **outbound__clientAuth__clientCert__source**.

| Možné hodnoty | Popis |
| ---------------- | ------------ |
| IoT Edge | Default (Výchozí). Používá certifikát identity modulu konfigurovaný nástrojem IoT Edge démon zabezpečení.

### <a name="outbound-server-authentication"></a>Ověřování odchozího serveru

Jeden z cílových typů pro předplatitele Event Grid je Webhook. Ve výchozím nastavení jsou pro tyto předplatitele přijímány pouze koncové body HTTPS.

Konfigurace pro řízení cílových **outbound__webhook__httpsOnly** zásad pro Webhook.

| Možné hodnoty | Popis |
| ----------------  | ------------ |
| true | Default (Výchozí). Povoluje pouze předplatitele s koncovým bodem HTTPS.
| false (nepravda) | Umožňuje odběratelům buď koncový bod HTTP, nebo HTTPS.

Ve výchozím nastavení Event Grid modul ověří certifikát serveru předplatitele. Ověřování můžete přeskočit přepsáním **outbound__webhook__skipServerCertValidation**. Možné hodnoty:

| Možné hodnoty | Popis |
| ----------------  | ------------ |
| true | Neověřovat certifikát serveru odběratele.
| false (nepravda) | Default (Výchozí). Ověří certifikát serveru odběratele.

Pokud je certifikát předplatitele podepsaný svým držitelem, pak ve výchozím nastavení Event Grid modul tyto předplatitele odmítne. Chcete-li umožnit certifikát podepsaný svým držitelem, můžete přepsat **outbound__webhook__allowUnknownCA**. Následující tabulka zachycuje možné hodnoty (y).

| Možné hodnoty | Popis |
| ----------------  | ------------ |
| true | Default (Výchozí). Umožňuje úspěšné předložení certifikátů podepsaných svým držitelem.
| false (nepravda) | Selže, pokud se zobrazí certifikáty podepsané svým držitelem.

>[!IMPORTANT]
>V produkčních scénářích budete chtít nastavit **outbound__webhook__allowUnknownCA** na **hodnotu NEPRAVDA**.

> [!NOTE]
>IoT Edge prostředí generuje certifikáty podepsané svým držitelem. Doporučujeme, abyste vygenerovali certifikáty vydávané autorizovanými certifikačními autoritami pro produkční úlohy a nastavili vlastnost **allowUnknownCA** u příchozích i odchozích na **hodnotu false**.

## <a name="summary"></a>Souhrn

Event Grid modul je **ve výchozím nastavení zabezpečený**. Pro produkční nasazení doporučujeme ponechat tato výchozí nastavení.

V následující části jsou uvedené principy GUID, které se použijí při konfiguraci:

* Povolí v modulu pouze požadavky HTTPS.
* Povolte pouze ověřování klientů na základě certifikátů. Povolte pouze ty certifikáty, které jsou vydány známými certifikačními autoritami. Zakažte certifikáty podepsané svým držitelem.
* Zakáže ověřování klienta založeného na SASKey.
* Při odchozích voláních vždy prezentovat certifikát identity modulu Event Grid.
* Povolí pouze předplatitelům HTTPS pro cílové typy Webhooku.
* Vždy ověřte certifikát serveru odběratele pro cílové typy Webhooku. Povolte pouze certifikáty vydané známými certifikačními autoritami. Zakažte certifikáty podepsané svým držitelem.

Ve výchozím nastavení je Event Grid modul nasazený s následující konfigurací:

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
