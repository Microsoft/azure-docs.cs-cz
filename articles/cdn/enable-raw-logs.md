---
title: Azure CDN HTTP nezpracované protokoly
description: Tento článek popisuje azure CDN HTTP nezpracované protokoly.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371630"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP nezpracované protokoly
Nezpracované protokoly poskytují bohaté informace o operacích a chybách, které jsou důležité pro auditování a řešení potíží. Nezpracované protokoly se liší od protokolů aktivit. Protokoly aktivit poskytují přehled o operacích provedených na prostředcích Azure. Nezpracované protokoly poskytují záznam o operacích prostředku.

> [!IMPORTANT]
> Funkce nezpracovaných protokolů HTTP je dostupná pro Azure CDN od Microsoftu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="configuration"></a>Konfigurace

Konfigurace nezpracovaných protokolů pro síť Azure CDN z profilu Microsoftu: 

1. V nabídce portálu Azure vyberte **všechny prostředky** >> **\<vašeho profilu CDN>**.

2. V části **Monitoring**vyberte **Nastavení diagnostiky**.

3. Vyberte **+ Přidat diagnostické nastavení**.

    ![Diagnostické nastavení CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Nezpracované protokoly jsou k dispozici pouze na úrovni profilu, zatímco agregované protokoly stavového kódu http jsou k dispozici na úrovni koncového bodu.

4. V části **Nastavení diagnostiky**zadejte název nastavení diagnostiky v části **Název nastavení diagnostiky**.

5. Vyberte **protokol** a nastavte uchovávání ve dnech.

6. Vyberte **podrobnosti o cíli**. Možnosti cíle jsou:
    * **Odeslání do Log Analytics**
        * Vyberte pracovní prostor **Odběr** a **Analýza protokolů**.
    * **Archivovat do účtu úložiště**
        * Vyberte **předplatné** a **účet úložiště**.
    * **Streamování do centra událostí**
        * Vyberte **předplatné**, **obor názvů centra událostí**, název centra událostí **(volitelný)** a **název zásad centra událostí**.

    ![Diagnostické nastavení CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Vyberte **Uložit**.

## <a name="raw-logs-properties"></a>Vlastnosti nezpracovaných protokolů

Azure CDN ze služby Microsoft Service aktuálně poskytuje nezpracované protokoly. Nezpracované protokoly poskytují jednotlivé požadavky rozhraní API s každou položkou s následujícím schématem: 

| Vlastnost              | Popis                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Jedinečný referenční řetězec, který identifikuje požadavek obsluhovaný front door, také odeslánjako x-Azure-ref záhlaví klientovi. Vyžadováno pro vyhledávání podrobností v protokolech přístupu pro konkrétní požadavek. |
| Metoda http            | Metoda HTTP použitá požadavkem.                                                                                                                                                                     |
| HttpVersion           | Typ požadavku nebo připojení.                                                                                                                                                                   |
| Requesturi            | Identifikátor URI přijatého požadavku.                                                                                                                                                                         |
| RequestBytes          | Velikost zprávy požadavku HTTP v bajtů, včetně hlavičky požadavku a tělo požadavku.                                                                                                   |
| Bajty odpovědí         | Bajty odeslané serverem back-end jako odpověď.                                                                                                                                                    |
| Useragent             | Typ prohlížeče, který klient použil.                                                                                                                                                               |
| ClientIp              | IP adresa klienta, který podal požadavek.                                                                                                                                                  |
| TimeTaken             | Doba, po kterou akce trvala v milisekundách.                                                                                                                                            |
| Protokol Zabezpečení      | Verze protokolu TLS/SSL používaná požadavkem nebo hodnotou null, pokud není šifrování.                                                                                                                           |
| Koncový bod              | Hostitel koncového bodu CDN byl nakonfigurován pod nadřazeným profilem CDN.                                                                                                                                   |
| Název hostitele back-endu     | Název hostitele back-endu nebo původu, kde jsou odesílány požadavky.                                                                                                                                |
| Odesláno do původního štítu | Pokud true, to znamená, že požadavek byl zodpovězen z mezipaměti původu štít unavumísto okraje pop. Štít původu je nadřazená mezipaměť používaná ke zlepšení poměru přístupů do mezipaměti.                                       |
| HttpStatusCode        | Stavový kód HTTP vrácený z proxy serveru.                                                                                                                                                        |
| HttpStatusDetails     | Výsledný stav na požadavku. Význam této hodnoty řetězce lze nalézt v tabulce odkaz na stav.                                                                                              |
| Pop                   | Okraj pop, který odpověděl na požadavek uživatele. Zkratky POP jsou letištní kódy příslušných metra.                                                                                   |
| Stav mezipaměti          | Označuje, pokud byl objekt vrácen z mezipaměti nebo pochází z počátku.                                                                                                             |
> [!IMPORTANT]
> Funkce protokoly HTTP Raw je automaticky k dispozici pro všechny profily vytvořené nebo aktualizované po **25.** U profilů CDN vytvořených dříve by měl být koncový bod CDN po nastavení protokolování aktualizován. Například lze přejít na geografické filtrování pod koncovými body CDN a zablokovat libovolnou zemi, která není relevantní pro jejich pracovní vytížení, a schůdky uložit. 

> [!NOTE]
> Protokoly lze zobrazit v profilu Log Analytics spuštěním dotazu. Ukázkový dotaz by vypadal jako AzureDiagnostics | kde kategorie == "AzureCdnAccessLog"

## <a name="next-steps"></a>Další kroky
V tomto článku jste povolili nezpracované protokoly HTTP pro službu Microsoft CDN.

Další informace o Azure CDN a dalších službách Azure uvedených v tomto článku najdete v následujících tématech:

* [Analyzovat](cdn-log-analysis.md) Vzory využití Azure CDN.

* Další informace o [Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/overview).

* Konfigurace [analýzy protokolů v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
