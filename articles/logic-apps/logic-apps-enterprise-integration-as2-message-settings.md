---
title: Nastavení zpráv AS2 – Azure Logic Apps
description: Referenční příručka pro AS2 odesílat a přijímat nastavení v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "63769448"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referenční informace pro nastavení zpráv AS2 v Azure Logic Apps sadou Enterprise Integration Pack

Tento odkaz popisuje vlastnosti, které můžete nastavit pro určení, jak smlouvy AS2 zpracovává zprávy odesílané nebo přijímané mezi obchodními partnery. Nastavení těchto vlastností na základě vaše smlouvy s partnerem, který vyměňuje zprávy s vámi.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Nastavení přijímání AS2

![Vyberte možnost "Zobrazit nastavení"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Vlastnost | Požaduje se | Popis |
|----------|----------|-------------|
| **Přepsat vlastnosti zprávy** | Ne | Přepsání vlastností příchozí zprávy s nastavením vlastností. |
| **Zprávu je nutné podepsat** | Ne | Určuje, zda všechny příchozí zprávy musí být digitálně podepsané. Pokud budete vyžadovat podepisování, z **certifikát** seznamu, vyberte existující hosta partnera veřejný certifikát pro ověření podpisu na zprávy. Pokud nemáte k dispozici certifikát, další informace o [přidání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Zprávu je nutné zašifrovat** | Ne | Určuje, zda všechny příchozí zprávy musí šifrovat. Zprávy šifrované bez odmítají. Pokud vyžadujete šifrování, z **certifikát** vyberte existující hostitele partnera privátní certifikát pro dešifrování příchozí zprávy. Pokud nemáte k dispozici certifikát, další informace o [přidání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Zprávu je nutné zkomprimovat** | Ne | Určuje, zda všechny příchozí zprávy musí být komprimovány. Bez komprimované zprávy jsou odmítnuta. |
| **Zakázat duplicity ID zpráv** | Ne | Určuje, jestli se má povolit zpráv s duplicitními identifikátory. Pokud zakážete duplicitní ID, vyberte počet dní mezi kontrolami. Můžete také zvolit, jestli se má pozastavit duplicitní položky. |
| **MDN Text** | Ne | Určuje výchozí zprávu dispozice oznámení (MDN), který chcete pro odesílatele zprávy. |
| **Odesílat zprávy MDN.** | Ne | Určuje, jestli se má odeslat synchronní něho pro přijaté zprávy.  |
| **Odeslat podepsanou zprávu MDN** | Ne | Určuje, jestli se má odeslat podepsaný něho pro přijaté zprávy. Pokud budete vyžadovat podepisování, z **algoritmus MIC** vyberte algoritmus použitý k podepsání zprávy. |
| **Odeslat asynchronní zprávu MDN** | Ne | Určuje, jestli se má odeslat něho asynchronně. Pokud vyberete v asynchronní něho **URL** pole, zadejte adresu URL pro kam poslat platbu něho. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Nastavení pro odesílání AS2

![Vyberte "Nastavení odesílání"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Vlastnost | Požaduje se | Popis |
|----------|----------|-------------|
| **Povolit podepisování zpráv** | Ne | Určuje, zda všechny odchozí zprávy musí být digitálně podepsané. Pokud budete potřebovat, podepsání, vyberte tyto hodnoty: <p>-Od **podpisový algoritmus** vyberte algoritmus použitý k podepsání zprávy. <br>-Od **certifikát** vyberte existující hostitele partnera privátní certifikát pro podepisování zpráv. Pokud nemáte k dispozici certifikát, další informace o [přidání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Povolit šifrování zpráv** | Ne | Určuje, zda všechny odchozí zprávy musí šifrovat. Pokud vyžadujete šifrování, vyberte tyto hodnoty: <p>-Od **šifrovací algoritmus** vyberte algoritmus hosta partnera veřejný certifikát pro šifrování zpráv. <br>-Od **certifikát** vyberte existující hosta partnera privátní certifikát pro šifrování odchozích zpráv. Pokud nemáte k dispozici certifikát, další informace o [přidání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Povolit kompresi zpráv** | Ne | Určuje, zda všechny odchozí zprávy musí být komprimovány. |
| **Rozbalit hlavičky HTTP** | Ne | Vloží HTTP `content-type` záhlaví na jednom řádku. |
| **Název souboru v hlavičce MIME přenosu** | Ne | Určuje, zda mají být zahrnuty název souboru hlavičky MIME. |
| **Požadovat zprávy MDN** | Ne | Určuje, zda chcete zobrazovat zprávy s oznámením dispozice (něho) pro všechny odchozí zprávy. |
| **Požadovat podepsané zprávy MDN** | Ne | Určuje, zda chcete zobrazovat podepsaný něho pro všechny odchozí zprávy. Pokud budete vyžadovat podepisování, z **algoritmus MIC** vyberte algoritmus použitý k podepsání zprávy. |
| **Požadovat asynchronní zprávy MDN** | Ne | Určuje, zda chcete zobrazovat něho asynchronně. Pokud vyberete v asynchronní něho **URL** pole, zadejte adresu URL pro kam poslat platbu něho. |
| **Povolit NRR** | Ne | Určuje, jestli se má vyžadovat-neodvolatelnost příjmu (NRR). Tento atribut komunikace poskytuje důkazy, který uživateli přišel data, jak je řešit. |
| **Formát algoritmu SHA2** | Ne | Určuje formát algoritmus MIC má použít pro podepisování v záhlaví pro odchozí zprávy AS2 nebo zprávy MDN. |
||||

## <a name="next-steps"></a>Další postup

[Výměna zpráv AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
