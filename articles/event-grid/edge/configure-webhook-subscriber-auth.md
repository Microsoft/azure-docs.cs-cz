---
title: Konfigurace ověřování předplatitele Webhooku – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurace ověřování odběratelů Webhooku
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992480"
---
# <a name="configure-webhook-subscriber-authentication"></a>Konfigurace ověřování odběratelů Webhooku

Tato příručka obsahuje příklady možných konfigurací předplatitele Webhooku pro modul Event Grid. Ve výchozím nastavení jsou pro předplatitele webhooků přijímány jenom koncové body HTTPS. Modul Event Grid se odmítne, pokud předplatitel prezentuje certifikát podepsaný svým držitelem.

## <a name="allow-only-https-subscriber"></a>Povoluje jenom odběratele HTTPS.

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Povolení odběratele HTTPS s certifikátem podepsaným svým držitelem

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Nastavte vlastnost `outbound:webhook:allowUnknownCA` tak, aby `true` jenom v testovacích prostředích, protože byste mohli obvykle používat certifikáty podepsané svým držitelem. Pro produkční úlohy doporučujeme, aby byly nastavené na **hodnotu NEPRAVDA**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Povolení odběratele HTTPS, ale přeskočení ověřování certifikátů

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Nastavte vlastnost `outbound:webhook:skipServerCertValidation` tak, aby `true` jenom v testovacích prostředích, protože možná neprezentujete certifikát, který je potřeba ověřit. U produkčních úloh doporučujeme, aby byly nastavené na **hodnotu NEPRAVDA** .

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Povolení HTTP i HTTPS s certifikáty podepsanými svým držitelem

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Nastavte vlastnost `outbound:webhook:httpsOnly` tak, aby `false` jenom v testovacích prostředích, protože byste nejdřív mohli vytvořit předplatitele HTTP. U produkčních úloh doporučujeme, aby se nastavily na **hodnotu true** .
