---
title: Azure CDN nezpracovaných protokolů HTTP
description: Tento článek popisuje Azure CDN nezpracované protokoly HTTP.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: a2522eba17574246ab99a0d47a42f128d5f61ace
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888636"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN nezpracovaných protokolů HTTP
Nezpracované protokoly poskytují obsáhlé informace o operacích a chybách, které jsou důležité pro auditování a řešení potíží. Nezpracované protokoly se liší od protokolů aktivit. Protokoly aktivit poskytují přehled o operacích provedených v prostředcích Azure. Nezpracované protokoly poskytují záznam o operacích s vaším prostředkem.

> [!IMPORTANT]
> Pro Azure CDN od Microsoftu je k dispozici funkce HTTP holých protokolů.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Konfigurace

Konfigurace nezpracovaných protokolů pro Azure CDN z profilu Microsoftu: 

1. V nabídce Azure Portal vyberte **všechny prostředky**  >>  **\<your-CDN-profile>** .

2. V části **monitorování**vyberte **nastavení diagnostiky**.

3. Vyberte **+ Přidat nastavení diagnostiky**.

    ![Nastavení diagnostiky CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Nezpracované protokoly jsou k dispozici pouze na úrovni profilu, přestože jsou k dispozici agregované protokoly stavového kódu HTTP na úrovni koncového bodu.

4. V části **nastavení diagnostiky**zadejte název nastavení diagnostiky v části **název nastavení diagnostiky**.

5. Vyberte **protokol** a nastavte dobu uchování ve dnech.

6. Vyberte **Podrobnosti o cíli**. Možnosti cíle jsou:
    * **Odeslání do Log Analytics**
        * Vyberte **předplatné** a **Log Analytics pracovní prostor**.
    * **Archivace do účtu úložiště**
        * Vyberte **předplatné** a **účet úložiště**.
    * **Streamování do centra událostí**
        * Vyberte **předplatné**, **obor názvů centra událostí**, **název centra událostí (nepovinné)** a **název zásady centra událostí**.

    ![Nastavení diagnostiky CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Vyberte **Uložit**.

## <a name="raw-logs-properties"></a>Nezpracované vlastnosti protokolů

Azure CDN ze služby společnosti Microsoft aktuálně poskytuje nezpracované protokoly. Nezpracované protokoly obsahují jednotlivé požadavky na rozhraní API s každou položkou, která má následující schéma: 

| Vlastnost              | Popis                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Jedinečný referenční řetězec, který identifikuje požadavek, který je obsluhován předními dvířky, je také odeslán jako hlavička X-Azure-ref na klienta. Vyžaduje se pro hledání podrobností v protokolech přístupu pro konkrétní požadavek. |
| HttpMethod            | Metoda HTTP, kterou požadavek používá                                                                                                                                                                     |
| HttpVersion           | Typ požadavku nebo připojení                                                                                                                                                                   |
| RequestUri            | Identifikátor URI přijatého požadavku                                                                                                                                                                         |
| RequestBytes          | Velikost zprávy požadavku HTTP v bajtech, včetně hlaviček požadavků a textu žádosti.                                                                                                   |
| ResponseBytes         | Bajty odeslané back-end serverem jako odpověď                                                                                                                                                    |
| UserAgent             | Typ prohlížeče, který klient použil.                                                                                                                                                               |
| ClientIp              | IP adresa klienta, který odeslal požadavek.                                                                                                                                                  |
| TimeTaken             | Doba, kterou trvala akce, v milisekundách.                                                                                                                                            |
| Tato SecurityProtocol      | Verze protokolu TLS/SSL používaná požadavkem nebo hodnotou null, pokud není šifrování.                                                                                                                           |
| Koncový bod              | Hostitel koncového bodu CDN je nakonfigurovaný v nadřazeném profilu CDN.                                                                                                                                   |
| Název hostitele back-endu     | Název hostitele back-endu nebo původ, ve kterém se požadavky odesílají.                                                                                                                                |
| Odesláno do počátečního štítku | Pokud má hodnotu true, znamená to, že žádost byla zodpovězena z počáteční mezipaměti ochrany před hraničním rozhraním pop. Počáteční ochrana je nadřazená mezipaměť, která se používá ke zvýšení poměru přístupů do mezipaměti.                                       |
| HttpStatusCode        | Stavový kód HTTP vrácený z proxy serveru.                                                                                                                                                        |
| HttpStatusDetails     | Výsledný stav žádosti. Význam této řetězcové hodnoty lze nalézt v tabulce odkazů na stav.                                                                                              |
| Výstrah                   | Bod POP hraničního zobrazení, který odpověděl na žádost uživatele Zkratky pro body POP jsou kódy letišť příslušných METROS.                                                                                   |
| Stav mezipaměti          | Označuje, zda byl objekt vrácen z mezipaměti nebo pochází od počátku.                                                                                                             |
> [!IMPORTANT]
> Funkce HTTP RAW logs je k dispozici automaticky pro všechny profily vytvořené nebo aktualizované po **25. únoru 2020**. Pro profily CDN, které jste vytvořili dřív, by se měl po nastavení protokolování aktualizovat koncový bod CDN. Například jedna může přejít na geografická filtrování v rámci koncových bodů CDN a blokovat každou zemi nebo oblast, která není relevantní pro své úlohy, a pak klikněte na Uložit. 

> [!NOTE]
> Protokoly lze zobrazit v rámci profilu Log Analytics spuštěním dotazu. Vzorový dotaz by vypadal jako AzureDiagnostics | WHERE kategorie = = "AzureCdnAccessLog"

## <a name="next-steps"></a>Další kroky
V tomto článku jste povolili protokoly HTTP Raw pro službu Microsoft CDN.

Další informace o Azure CDN a dalších službách Azure, které jsou uvedené v tomto článku, najdete v tématech:

* [Analyzovat](cdn-log-analysis.md) Azure CDN způsoby použití.

* Přečtěte si další informace o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Nakonfigurujte [Log Analytics v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
