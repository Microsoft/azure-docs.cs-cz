---
title: Konfigurace ověřování předplatitele Webhooku – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurace ověřování odběratelů webhooků
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d78b6e80005e9533ccd5ebfaea853f35ae6004be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171648"
---
# <a name="configure-webhook-subscriber-authentication"></a>Konfigurace ověřování odběratelů webhooků

Tato příručka obsahuje příklady možných konfigurací předplatitele Webhooku pro modul Event Grid. Ve výchozím nastavení jsou pro předplatitele webhooků přijímány jenom koncové body HTTPS. Modul Event Grid se odmítne, pokud předplatitel prezentuje certifikát podepsaný svým držitelem.

## <a name="allow-only-https-subscriber"></a>Povoluje jenom odběratele HTTPS.

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Povolení odběratele HTTPS s certifikátem podepsaným svým držitelem

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Nastavte vlastnost `outbound__webhook__allowUnknownCA` na `true` pouze v testovacích prostředích, protože obvykle používáte certifikáty podepsané svým držitelem. Pro produkční úlohy doporučujeme, aby byly nastavené na **hodnotu NEPRAVDA**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Povolení odběratele HTTPS, ale přeskočení ověřování certifikátů

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Nastavte vlastnost `outbound__webhook__skipServerCertValidation` jenom na `true` testovací prostředí, protože nebudete mít certifikát, který je potřeba ověřit. U produkčních úloh doporučujeme, aby byly nastavené na **hodnotu NEPRAVDA** .

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Povolení HTTP i HTTPS s certifikáty podepsanými svým držitelem

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Nastavte vlastnost `outbound__webhook__httpsOnly` pouze na `false` v testovacích prostředích, protože můžete chtít nejdřív uvést předplatitele http. U produkčních úloh doporučujeme, aby se nastavily na **hodnotu true** .
