---
title: Zabezpečení a ověřování – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Zabezpečení a ověřování v programu Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844509"
---
# <a name="security-and-authentication"></a>Zabezpečení a ověřování

Zabezpečení a ověřování je pokročilý koncept a vyžaduje nejprve znalost základů event gridu. Začněte [zde,](concepts.md) pokud jste novým event gridem na IoT Edge. Modul Event Grid staví na existující infrastruktuře zabezpečení na IoT Edge. Podrobnosti a nastavení naleznete v [této dokumentaci.](../../iot-edge/security.md)

Následující části podrobně popisují, jak jsou tato nastavení zabezpečena a ověřena:

* Konfigurace TLS
* Ověření příchozího klienta
* Ověřování odchozího serveru
* Ověřování odchozího klienta

>[!IMPORTANT]
>Zabezpečení modulu Event Grid a pákový efekt ověřování je stávající infrastruktura dostupná na IoT Edge. Předpokládá se, že podsystém IoT Edge je zabezpečený.

>[!IMPORTANT]
>Konfigurace event gridu je **ve výchozím nastavení zabezpečená**. V následujících podčástech jsou vysvětleny všechny možnosti a možné hodnoty, které můžete použít k přepsání aspektů ověřování. Před provedením jakýchkoli změn pochopte dopad. Aby se změny projevily, bude nutné modul Event Grid znovu nasadit.

## <a name="tls-configuration-aka-server-authentication"></a>Konfigurace TLS (a.k.a. ověřování serveru)

Modul Event Grid hostuje koncové body PROTOKOLU HTTP i HTTPS. Každému modulu IoT Edge je přiřazen certifikát serveru bezpečnostním demonem IoT Edge. K zabezpečení koncového bodu používáme certifikát serveru. Po vypršení platnosti se modul automaticky aktualizuje pomocí nového certifikátu od demonu zabezpečení IoT Edge.

Ve výchozím nastavení je povolena pouze komunikace HTTPS. Toto chování můžete přepsat pomocí **konfigurace inbound__serverAuth__tlsPolicy.** V následující tabulce jsou zachyceny možné hodnoty této vlastnosti.

| Možné hodnoty | Popis |
| ---------------- | ------------ |
| Striktní | Default (Výchozí). Povolí pouze protokol HTTPS.
| Povoleno | Povolí protokol HTTP i PROTOKOL HTTPS.
| Zakázáno | Povolí pouze protokol HTTP.

## <a name="inbound-client-authentication"></a>Ověření příchozího klienta

Klienti jsou entity provádějící operace správy nebo běhu. Klienty mohou být další moduly IoT Edge, jiné aplikace než IoT.

Modul Event Grid podporuje dva typy ověřování klientů:

* Sdílený přístupový podpis (SAS) založený na klíči
* na základě certifikátu

Ve výchozím nastavení je modul Event Grid nakonfigurován tak, aby přijímal pouze ověřování založené na certifikátech. Při spuštění modul Event Grid načte "TrustBundle" z daemonu zabezpečení IoT Edge a použije jej k ověření libovolného klientského certifikátu. Klientské certifikáty, které se nepřevedou `UnAuthorized`do tohoto řetězce, budou odmítnuty pomocí aplikace .

### <a name="certificate-based-client-authentication"></a>Ověřování klientů založené na certifikátech

Ověřování na základě certifikátu je ve výchozím nastavení zapnuto. Ověřování na základě certifikátů můžete zakázat prostřednictvím vlastnosti **inbound__clientAuth__clientCert__enabled**. V následující tabulce jsou zachyceny možné hodnoty.

| Možné hodnoty | Popis |
| ----------------  | ------------ |
| true | Default (Výchozí). Vyžaduje všechny požadavky do modulu Event Grid k předložení klientského certifikátu. Kromě toho budete muset nakonfigurovat **inbound__clientAuth__clientCert__source**.
| false (nepravda) | Nenuťte klienta předložit certifikát.

Následující tabulka zachycuje možné hodnoty pro **inbound__clientAuth__clientCert__source**

| Možné hodnoty | Popis |
| ---------------- | ------------ |
| IoT Edge | Default (Výchozí). Používá fondový balíček IoT Edge k ověření všech klientských certifikátů.

Pokud klient předloží autosignaci, modul Event Grid ve výchozím nastavení takové požadavky odmítne. Klientské certifikáty podepsané svým držitelem můžete povolit prostřednictvím **inbound__clientAuth__clientCert__allowUnknownCA** vlastnosti. V následující tabulce jsou zachyceny možné hodnoty.

| Možné hodnoty | Popis |
| ----------------  | ------------|
| true | Default (Výchozí). Umožňuje úspěšné předložení certifikátů podepsaných svým držitelem.
| false (nepravda) | Bude selhat požadavky, pokud jsou předloženy certifikáty podepsané svým držitelem.

>[!IMPORTANT]
>V produkčních scénářích můžete nastavit **inbound__clientAuth__clientCert__allowUnknownCA** na **false**.

### <a name="sas-key-based-client-authentication"></a>Ověřování klienta na základě klíče SAS

Kromě ověřování na základě certifikátu může modul Event Grid také provést ověřování pomocí klíče SAS. Klíč SAS je jako tajný klíč nakonfigurovaný v modulu Event Grid, který by měl použít k ověření všech příchozích volání. Klienti musí zadat tajný klíč v hlavičce HTTP 'aeg-sas-key'. Žádost bude odmítnuta, `UnAuthorized` pokud se neshoduje.

Konfigurace pro řízení ověřování pomocí klíče SAS je **inbound__clientAuth__sasKeys__enabled**.

| Možné hodnoty | Popis  |
| ----------------  | ------------ |
| true | Umožňuje ověřování pomocí klíče SAS. Vyžaduje **inbound__clientAuth__sasKeys__key1** nebo **inbound__clientAuth__sasKeys__key2**
| false (nepravda) | Default (Výchozí). Ověřování založené na klíči SAS je zakázáno.

 **inbound__clientAuth__sasKeys__key1** a **inbound__clientAuth__sasKeys__key2** jsou klíče, které nakonfigurujete modul Mřížka událostí pro kontrolu proti příchozím požadavkům. Alespoň jeden z klíčů je třeba nakonfigurovat. Klient, který požádá o požadavek, bude muset předložit klíč jako součást hlavičky požadavku**aeg-sas-key**. Pokud jsou oba klíče nakonfigurovány, klient může prezentovat jeden z klíčů.

> [!NOTE]
>Můžete nakonfigurovat obě metody ověřování. V takovém případě je klíč SAS nejprve zkontrolován a pouze v případě, že se nezdaří, je provedeno ověřování na základě certifikátu. Aby byl požadavek úspěšný, musí být úspěšná pouze jedna z metod ověřování.

## <a name="outbound-client-authentication"></a>Ověřování odchozího klienta

Klient v odchozím kontextu odkazuje na modul Event Grid. Operace, která se provádí, doručuje události odběratelům. Moduly pro přihlášení k odběru jsou považovány za server.

Každému modulu IoT Edge je přiřazen certifikát identity bezpečnostním demonem IoT Edge. Pro odchozí hovory používáme certifikát totožnosti. Po vypršení platnosti se modul automaticky aktualizuje pomocí nového certifikátu od demonu zabezpečení IoT Edge.

Konfigurace pro řízení ověřování odchozího klienta je **outbound__clientAuth__clientCert__enabled**.

| Možné hodnoty | Popis |
| ----------------  | ------------ |
| true | Default (Výchozí). Vyžaduje všechny odchozí požadavky z modulu Event Grid k předložení certifikátu. Je třeba nakonfigurovat **outbound__clientAuth__clientCert__source**.
| false (nepravda) | Nevyžadují modul Event Grid k předložení jeho certifikátu.

Konfigurace, která řídí zdroj **certifikátu,** je outbound__clientAuth__clientCert__source .

| Možné hodnoty | Popis |
| ---------------- | ------------ |
| IoT Edge | Default (Výchozí). Používá certifikát identity modulu nakonfigurovaný daemonem zabezpečení IoT Edge.

### <a name="outbound-server-authentication"></a>Ověřování odchozího serveru

Jedním z cílových typů pro předplatitele event gridu je "Webhook". Ve výchozím nastavení jsou pro tyto předplatitele přijímány pouze koncové body HTTPS.

Konfigurace pro řízení zásad y cíle webhooku **outbound__webhook__httpsOnly**.

| Možné hodnoty | Popis |
| ----------------  | ------------ |
| true | Default (Výchozí). Umožňuje pouze předplatitele s koncovým bodem HTTPS.
| false (nepravda) | Umožňuje předplatitelům s koncovým bodem HTTP nebo HTTPS.

Ve výchozím nastavení modul Event Grid ověří certifikát serveru odběratele. Ověření můžete přeskočit přepsáním **outbound__webhook__skipServerCertValidation**. Možné hodnoty:

| Možné hodnoty | Popis |
| ----------------  | ------------ |
| true | Neověřujte certifikát serveru odběratele.
| false (nepravda) | Default (Výchozí). Ověřte certifikát serveru odběratele.

Pokud je certifikát odběratele podepsán vlastním podpisem, pak ve výchozím nastavení modul Event Grid tyto předplatitele odmítne. Chcete-li povolit certifikát podepsaný svým držitelem, můžete přepsat **outbound__webhook__allowUnknownCA**. V následující tabulce jsou zachyceny možné hodnoty.

| Možné hodnoty | Popis |
| ----------------  | ------------ |
| true | Default (Výchozí). Umožňuje úspěšné předložení certifikátů podepsaných svým držitelem.
| false (nepravda) | Bude selhat požadavky, pokud jsou předloženy certifikáty podepsané svým držitelem.

>[!IMPORTANT]
>V produkčních scénářích budete chtít nastavit **outbound__webhook__allowUnknownCA** na **false**.

> [!NOTE]
>Prostředí IoT Edge generuje certifikáty podepsané svým držitelem. Doporučení je generovat certifikáty vydané autorizovanými certifikačními autoritami pro produkční úlohy a nastavit **vlastnost allowUnknownCA** na příchozí i odchozí na **false**.

## <a name="summary"></a>Souhrn

Modul Event Grid je **ve výchozím nastavení zabezpečený**. Doporučujeme zachovat tyto výchozí hodnoty pro vaše produkční nasazení.

Níže jsou uvedeny hlavní zásady, které je třeba použít při konfiguraci:

* Povolit pouze požadavky HTTPS do modulu.
* Povolit pouze ověřování klientů na základě certifikátu. Povolte pouze ty certifikáty vydané známými certifikačními autoritami. Zakázat certifikáty podepsané svým držitelem.
* Zakázat ověřování klienta na základě saskey.
* Vždy prezentujte certifikát identity modulu Event Grid při odchozích hovorech.
* Povolit pouze předplatitele HTTPS pro typy cílů Webhook.
* Vždy ověřte certifikát serveru odběratele pro cílové typy Webhooku. Povolit pouze certifikáty vydané známými certifikačními autoritami. Zakázat certifikáty podepsané svým držitelem.

Ve výchozím nastavení je modul Event Grid nasazen s následující konfigurací:

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
