---
title: Azure Defender pro App Service – výhody a funkce
description: Přečtěte si o možnostech Azure Defenderu pro App Service a o tom, jak ho ve svém předplatném povolit.
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ec4ac5d355266a46b33d89fd25c2665493773f5d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100810"
---
# <a name="protect-your-web-apps-and-apis"></a>Ochrana webových aplikací a rozhraní API

## <a name="prerequisites"></a>Požadavky

Security Center je nativně integrovaná s App Service a odstraňuje nutnost nasazení a registraci – integrace je transparentní.

K ochraně Azure App Service plánu pomocí Azure Defenderu pro App Service budete potřebovat:

- Podporovaný plán App Service přidružený k vyhrazeným počítačům. Podporované plány jsou uvedeny v [dostupnosti](#availability).

- Ve vašem předplatném je povolený Azure Defender, jak je popsáno v tématu [rychlý Start: povolení Azure Defenderu](enable-azure-defender.md)

    > [!TIP]
    > Volitelně můžete v Azure Defenderu povolit jednotlivé plány (například Azure Defender pro App Service).

## <a name="availability"></a>Dostupnost

| Aspekt                       | Podrobnosti                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stav vydaných verzí:               | Obecná dostupnost (GA)                                                                                                                                                                      |
| Stanov                     | [Azure Defender pro App Service](azure-defender.md) se účtuje tak, jak je znázorněno na [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/) .<br>Fakturace se provádí na základě celkových výpočetních instancí ve všech plánech.       |
| Podporované plány App Service: | [Všechny plány App Service](https://azure.microsoft.com/pricing/details/app-service/plans/) jsou podporovány s výjimkou [Azure Functions v plánu spotřeby](../azure-functions/functions-scale.md). |
| Cloud                      | ![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![Ne](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Jaké jsou výhody Azure Defenderu pro App Service?

Azure App Service je plně spravovaná platforma pro vytváření a hostování webových aplikací a rozhraní API. Vzhledem k tomu, že je platforma plně spravovaná, nemusíte se starat o infrastrukturu. Poskytuje správu, monitorování a Operational Insights pro splnění požadavků na výkon, zabezpečení a dodržování předpisů na podnikové úrovni. Další informace najdete v tématu [Azure App Service](https://azure.microsoft.com/services/app-service/).

**Azure Defender pro App Service** využívá škálování cloudu k identifikaci útoků cílících na aplikace běžící přes App Service. Útočníci hledají webové aplikace a využívají slabých míst. Před směrováním do konkrétních prostředí požadavky na aplikace běžící v Azure procházejí několika branami, kde jsou zkontrolovány a protokolovány. Tato data se pak používají k identifikaci zneužití a útočníků a k získání nových vzorů, které se použijí později.

Když povolíte Azure Defender pro App Service, okamžitě získáte výhody z následujících služeb, které nabízí tento plán Azure Defenderu:

- **Secure** -Security Center posuzuje prostředky, které jsou pokryté vaším plánem App Service, a vygeneruje doporučení zabezpečení na základě jejich zjištění. Pomocí podrobných pokynů v těchto doporučeních můžete posílit své App Service prostředky.

- **Detekce** – Azure Defender detekuje množství hrozeb pro vaše App Service prostředky monitorováním:
    - instance virtuálního počítače, ve které je spuštěný App Service, a rozhraní pro správu
    - žádosti a odpovědi odeslané do aplikací App Service a z nich
    - základní izolované prostory a virtuální počítače
    - App Service interních protokolů – k dispozici díky viditelnosti, kterou má Azure jako poskytovatel cloudu

V rámci cloudového nativního řešení může Azure Defender identifikovat metodiky útoku, které se použijí na více cílů. Například z jednoho hostitele by bylo obtížné identifikovat distribuovaný útok z malé podmnožiny IP adres, procházením do podobných koncových bodů na více hostitelích.

Data protokolu a infrastruktura společně mohou sdělit příběh: od nového útoku, který je v nevolném stupni, aby mohl ohrozit zabezpečení počítačů zákazníků. Proto i v případě, že je Security Center nasazena po zneužití webové aplikace, může být schopna detekovat průběžné útoky.


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Jaké hrozby může Azure Defender pro App Service rozpoznat?

### <a name="threats-by-mitre-attck-tactics"></a>Hrozby pomocí MITRE ATT&CK taktiku

Azure Defender monitoruje mnoho hrozeb pro vaše App Service prostředky. Výstrahy se týkají téměř kompletního seznamu MITRE ATT&CK taktiku z předběžného útoku na příkazy a řízení. Azure Defender dokáže zjistit:

- **Hrozby před útoky** – Defender dokáže detekovat spuštění více typů skenerů ohrožení zabezpečení, které útočníci často používají k testování slabých a slabých verzí aplikací.

- **Počáteční hrozby přístupu**  -  [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) provádí tyto výstrahy, které zahrnují aktivaci výstrahy, když se známá škodlivá IP adresa připojí k vašemu Azure App Servicemu rozhraní FTP.

- **Hrozby spuštění** – Defender může detekovat pokusy o spuštění příkazů s vysokým oprávněním, příkazy pro Linux ve Windows App Service, chování útoku bez souborů, nástrojů pro dolování v digitální měně a mnoho dalších podezřelých a škodlivých aktivit provádění kódu.

### <a name="dangling-dns-detection"></a>Dangling DNS – detekce

Azure Defender pro App Service taky při vyřazení webu z provozu App Service identifikuje všechny položky DNS, které jsou v registrátoru DNS zbývající – tyto položky se nazývají dangling DNS. Když odeberete web a neodeberete jeho vlastní doménu z registrátora DNS, položka DNS odkazuje na neexistující prostředek a vaše subdoména je zranitelná vůči převzetí. Azure Defender nehledá v případě *existujících* položek DNS v dangling v registrátoru DNS. upozorní vás, když se vyřadí App Service web a jeho vlastní doména (položka DNS) se neodstraní.

Převzetí subdomény jsou společnou vysoce závažnou hrozbou pro organizace. Když objekt actor actor detekuje položku DNS dangling, vytvoří vlastní web na cílové adrese. Provoz určený pro doménu organizace se pak přesměruje na web herců hrozeb a může tento provoz použít pro nejrůznější škodlivou aktivitu.

Ochrana DNS dangling je dostupná bez ohledu na to, jestli jsou vaše domény spravované pomocí Azure DNS nebo externího registrátora domény a platí pro App Service v systémech Windows i Linux.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Příklad výstrahy v programu Azure Defender o zjištěné položce DNS v dangling Povolit Azure Defender pro App Service pro příjem této a další výstrahy pro vaše prostředí." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

Přečtěte si další informace o dangling DNS a hrozbě převzetí subdomény, v části [prevence danglingch záznamů DNS a zamezení převzetí subdomény](../security/fundamentals/subdomain-takeover.md).

Úplný seznam výstrah Azure App Service naleznete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureappserv).

> [!NOTE]
> Pokud vaše vlastní doména neodkazuje přímo na prostředek App Service nebo pokud program Defender nemonitoroval provoz na váš web, protože byla povolena ochrana DNS dangling (protože nebudou k dispozici protokoly pro identifikaci vlastní domény), program Defender nemusí aktivovat výstrahy dangling DNS.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro App Service. 

Související materiály najdete v následujících článcích: 

- Pokud chcete exportovat výstrahy do Azure Sentinel, všech SIEM třetích stran nebo jakýchkoli jiných externích nástrojů, postupujte podle pokynů v tématu [výstrahy streamování do řešení pro správu služeb Siem, společnosti nebo IT](export-to-siem.md).
- Seznam upozornění na App Service Azure Defenderu najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureappserv).
- Další informace o plánech App Service najdete v tématu [plány App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).
> [!div class="nextstepaction"]
> [Povolení Azure Defenderu](enable-azure-defender.md)