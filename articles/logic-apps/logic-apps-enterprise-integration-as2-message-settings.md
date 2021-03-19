---
title: Nastavení zpráv AS2
description: Referenční příručka pro AS2 nastavení odesílání a přijímání v Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "74793024"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referenční informace k nastavení zpráv AS2 v Azure Logic Apps s Enterprise Integration Pack

Tento odkaz popisuje vlastnosti, které můžete nastavit pro určení způsobu, jakým AS2 smlouva zpracovává zprávy odesílané a přijímané mezi obchodními partnery. Nastavte tyto vlastnosti podle vaší smlouvy s partnerem, který s vámi vyměňuje zprávy.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Nastavení příjmu AS2

![Vyberte přijmout nastavení.](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Vlastnost | Povinné | Popis |
|----------|----------|-------------|
| **Přepsat vlastnosti zprávy** | No | Potlačí vlastnosti příchozích zpráv s nastavením vlastností. |
| **Zpráva by měla být podepsaná.** | No | Určuje, zda musí být všechny příchozí zprávy digitálně podepsány. Pokud požadujete podepisování, vyberte ze seznamu **certifikát** stávající veřejný certifikát hostovaného partnera pro ověření podpisu zpráv. Pokud certifikát nemáte, přečtěte si další informace o [Přidávání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Zpráva by měla být zašifrovaná.** | No | Určuje, zda musí být všechny příchozí zprávy zašifrovány. Nešifrované zprávy jsou odmítnuty. Pokud vyžadujete šifrování, vyberte ze seznamu **certifikát** stávající privátní certifikát hostitelského partnera pro dešifrování příchozích zpráv. Pokud certifikát nemáte, přečtěte si další informace o [Přidávání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Zpráva by měla být komprimovaná** | No | Určuje, zda musí být všechny příchozí zprávy komprimovány. Nekomprimované zprávy jsou odmítnuty. |
| **Zakázat duplicity ID zpráv** | No | Určuje, zda mají být povoleny zprávy s duplicitními identifikátory. Pokud zakážete duplicitní identifikátory, vyberte počet dní mezi kontrolami. Můžete také zvolit, zda chcete pozastavit duplicity. |
| **MDN text** | No | Určuje výchozí oznámení o zařazování zpráv (MDN), které chcete odeslat odesílateli zprávy. |
| **Odeslat MDN** | No | Určuje, zda se mají pro přijaté zprávy odesílat synchronní MDNs.  |
| **Poslat podepsané MDN** | No | Určuje, zda se mají pro přijaté zprávy odesílat podepsané MDNs. Pokud požadujete podepisování, vyberte v seznamu **algoritmus mikrofonu** algoritmus, který se má použít pro podepisování zpráv. |
| **Odeslat asynchronní MDN** | No | Určuje, zda se má MDNs odeslat asynchronně. Pokud vyberete možnost asynchronní MDNs, v poli **Adresa URL** zadejte adresu URL, kam se má MDNs odeslat. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Nastavení odesílání AS2

![Vyberte Odeslat nastavení.](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Vlastnost | Povinné | Popis |
|----------|----------|-------------|
| **Povolit podepisování zpráv** | No | Určuje, jestli všechny odchozí zprávy musí být digitálně podepsané. Pokud požadujete podepisování, vyberte tyto hodnoty: <p>– Ze seznamu **podpisového algoritmu** vyberte algoritmus, který se má použít pro podepisování zpráv. <br>– V seznamu **certifikát** vyberte existující privátní certifikát hostitelského partnera pro podpisové zprávy. Pokud certifikát nemáte, přečtěte si další informace o [Přidávání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Povolit šifrování zpráv** | No | Určuje, jestli se všechny odchozí zprávy musí šifrovat. Pokud vyžadujete šifrování, vyberte tyto hodnoty: <p>– V seznamu **šifrovací algoritmus** vyberte algoritmus veřejného certifikátu hostovaného partnera hosta, který se použije k šifrování zpráv. <br>– Ze seznamu **certifikát** vyberte existující privátní certifikát hostovaného partnera pro šifrování odchozích zpráv. Pokud certifikát nemáte, přečtěte si další informace o [Přidávání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Povolit kompresi zprávy** | No | Určuje, zda musí být všechny odchozí zprávy komprimovány. |
| **Odložení hlaviček protokolu HTTP** | No | Vloží hlavičku HTTP na `content-type` jeden řádek. |
| **Název souboru přenosu v záhlaví MIME** | No | Určuje, jestli se má do záhlaví MIME zahrnout název souboru. |
| **MDN žádosti** | No | Určuje, jestli se mají dostávat oznámení o zařazování zpráv (MDNs) pro všechny odchozí zprávy. |
| **Žádost podepsaná MDN** | No | Určuje, jestli se má pro všechny odchozí zprávy přijímat podepsané MDNs. Pokud požadujete podepisování, vyberte v seznamu **algoritmus mikrofonu** algoritmus, který se má použít pro podepisování zpráv. |
| **Požadavek na asynchronní MDN** | No | Určuje, zda se má přijímat MDNs asynchronně. Pokud vyberete možnost asynchronní MDNs, v poli **Adresa URL** zadejte adresu URL, kam se má MDNs odeslat. |
| **Povolit NRR** | No | Určuje, jestli se má vyžadovat příjem bez odmítnutí (NRR). Tento atribut komunikace poskytuje důkaz o tom, že data byla přijata jako adresovaná. |
| **Formát algoritmu SHA2** | No | Určuje formát algoritmu MIC, který se má použít pro podepisování hlaviček odchozích zpráv AS2 nebo MDN. |
||||

## <a name="next-steps"></a>Další kroky

[Výměna zpráv AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
