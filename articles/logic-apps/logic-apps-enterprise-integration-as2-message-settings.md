---
title: Nastavení zpráv AS2
description: Referenční příručka pro nastavení odesílání a přijímání AS2 v Logic Apps Azure s podnikovou integrací
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793024"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Odkaz na nastavení zpráv AS2 v Logic Apps Azure s enterprise integration pack

Tento odkaz popisuje vlastnosti, které můžete nastavit pro určení způsobu, jakým smlouva AS2 zpracovává zprávy odeslané a přijaté mezi obchodními partnery. Nastavte tyto vlastnosti na základě vaší smlouvy s partnerem, který si s vámi vyměňuje zprávy.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 Nastavení příjmu

![Vyberte možnost "Nastavení příjmu"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Vlastnost | Požaduje se | Popis |
|----------|----------|-------------|
| **Přepsat vlastnosti zprávy** | Ne | Přepíše vlastnosti příchozích zpráv nastavením vlastností. |
| **Zpráva by měla být podepsána.** | Ne | Určuje, zda musí být všechny příchozí zprávy digitálně podepsány. Pokud požadujete podepsání, vyberte ze seznamu **Certifikát** y existující veřejný certifikát partnera hosta pro ověření podpisu ve zprávách. Pokud certifikát nemáte, přečtěte si další informace o [přidávání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Zpráva by měla být zašifrována** | Ne | Určuje, zda musí být všechny příchozí zprávy zašifrovány. Nešifrované zprávy jsou odmítnuty. Pokud požadujete šifrování, vyberte ze seznamu **Certifikáty** existující soukromý certifikát hostitelského partnera pro dešifrování příchozích zpráv. Pokud certifikát nemáte, přečtěte si další informace o [přidávání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Zpráva by měla být komprimována** | Ne | Určuje, zda musí být všechny příchozí zprávy komprimovány. Nekomprimované zprávy jsou odmítnuty. |
| **Zakázat duplikáty ID zprávy** | Ne | Určuje, zda mají být zprávy povoleny s duplicitnímI ID. Pokud zakážete duplicitní ID, vyberte počet dní mezi kontrolami. Můžete také zvolit, zda chcete pozastavit duplikáty. |
| **MDN Text** | Ne | Určuje výchozí oznámení o dispozičním řešení zprávy (MDN), které chcete odeslat odesílateli zprávy. |
| **Odeslat MDN** | Ne | Určuje, zda mají být pro přijaté zprávy odesílány synchronní skupiny MDN.  |
| **Odeslat podepsané MDN** | Ne | Určuje, zda má být pro přijaté zprávy odeslány podepsané skupiny MDN. Pokud požadujete podepisování, vyberte ze seznamu **Algoritmus MIC** algoritmus algoritmus, který chcete použít pro podepisování zpráv. |
| **Odeslat asynchronní MDN** | Ne | Určuje, zda mají být asynchronně odesílány soubory MDN. Pokud vyberete asynchronní mdns, v poli **URL** zadejte adresu URL, kam mají být soubory MDN odeslány. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 Nastavení odesílání

![Vyberte možnost Odeslat nastavení.](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Vlastnost | Požaduje se | Popis |
|----------|----------|-------------|
| **Povolení podepisování zpráv** | Ne | Určuje, zda musí být všechny odchozí zprávy digitálně podepsány. Pokud požadujete podepsání, vyberte tyto hodnoty: <p>- Ze seznamu **Podpisový algoritmus** vyberte algoritmus, který chcete použít pro podepisování zpráv. <br>- Ze seznamu **certifikátů** vyberte existující soukromý certifikát hostitelského partnera pro podepisování zpráv. Pokud certifikát nemáte, přečtěte si další informace o [přidávání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Povolení šifrování zpráv** | Ne | Určuje, zda musí být všechny odchozí zprávy zašifrovány. Pokud požadujete šifrování, vyberte tyto hodnoty: <p>- Ze seznamu **Šifrovací algoritmus** vyberte algoritmus veřejného certifikátu hosta partnera, který se má použít pro šifrování zpráv. <br>- Ze seznamu **certifikátů** vyberte existující soukromý certifikát partnera hosta pro šifrování odchozích zpráv. Pokud certifikát nemáte, přečtěte si další informace o [přidávání certifikátů](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Povolení komprese zpráv** | Ne | Určuje, zda musí být všechny odchozí zprávy komprimovány. |
| **Rozvinout záhlaví HTTP** | Ne | Vloží hlavičku HTTP `content-type` na jeden řádek. |
| **Přenos názvu souboru v hlavičce MIME** | Ne | Určuje, zda má být název souboru zahrnut do hlavičky MIME. |
| **Požadavek na vícenežnové informace** | Ne | Určuje, zda má být pro všechny odchozí zprávy přijímána oznámení o dispozičním řešení zpráv (MDN). |
| **Požadavek na podepsané MDN** | Ne | Určuje, zda mají být pro všechny odchozí zprávy přijímány podepsané skupiny MDN. Pokud požadujete podepisování, vyberte ze seznamu **Algoritmus MIC** algoritmus algoritmus, který chcete použít pro podepisování zpráv. |
| **Požadavek na asynchronní MDN** | Ne | Určuje, zda má být příjem mdna synchronně. Pokud vyberete asynchronní mdns, v poli **URL** zadejte adresu URL, kam mají být soubory MDN odeslány. |
| **Povolit nrr** | Ne | Určuje, zda má být vyžadována potvrzení o odmítnutí (NRR). Tento atribut komunikace poskytuje důkaz, že data byla přijata jako adresované. |
| **Formát algoritmu SHA2** | Ne | Určuje formát algoritmu MIC, který se má použít pro podepisování v záhlaví odchozích zpráv AS2 nebo MDN. |
||||

## <a name="next-steps"></a>Další kroky

[Výměna zpráv AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
