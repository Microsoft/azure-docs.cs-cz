---
title: Průvodce odstraňováním potíží – Azure DNS
description: V této cestě výukového programu začněte řešit běžné problémy s Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: fae63c61949302e25c9dee2899577fa4f0d2a975
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965574"
---
# <a name="azure-dns-troubleshooting-guide"></a>Průvodce řešením problémů pro Azure DNS

V tomto článku najdete informace o řešení běžných otázek Azure DNS.

Pokud tyto kroky problém nevyřeší, můžete také vyhledat nebo odeslat svůj problém na našem webu [Microsoft Q&na stránce s dotazy pro podporu komunity](/answers/topics/azure-virtual-network.html). Případně můžete otevřít žádost o podporu Azure.


## <a name="i-cant-create-a-dns-zone"></a>Nemůžu vytvořit zónu DNS

Při řešení běžných problémů zkuste použít jeden nebo několik následujících kroků:

1.  Zkontrolujte Azure DNS protokoly auditu a určete příčinu selhání.
2.  Název každé zóny DNS musí být v rámci dané skupiny prostředků jedinečný. To znamená, že dvě zóny DNS se stejným názvem nemůžou sdílet skupinu prostředků. Zkuste použít jiný název zóny nebo jinou skupinu prostředků.
3.  Může se zobrazit chyba typu Dosáhli nebo přesáhli jste maximální počet zón v předplatném {id předplatného}. Použijte jiné předplatné Azure, odstraňte některé zóny nebo kontaktujte podporu Azure se žádostí o zvýšení limitu vašeho předplatného.
4.  Může se zobrazit chyba typu Zóna {název zóny} není k dispozici. Tato chyba znamená, že Azure DNS se pro tuto zónu DNS nepodařilo přidělit názvové servery. Zkuste použít jiný název zóny. Nebo, pokud jste vlastníkem názvu domény, můžete kontaktovat podporu Azure, která vám pomůžou přidělit názvové servery za vás.


### <a name="recommended-articles"></a>Doporučené články

* [Záznamy a zóny DNS](dns-zones-records.md)
* [Vytvoření zóny DNS](./dns-getstarted-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Nejde mi vytvořit záznam DNS

Při řešení běžných problémů zkuste použít jeden nebo několik následujících kroků:

1.  Zkontrolujte Azure DNS protokoly auditu a určete příčinu selhání.
2.  Už tato sada záznamů existuje?  Azure DNS spravuje záznamy pomocí *sad* záznamů. To jsou kolekce záznamů se stejným názvem a typem. Pokud záznam se stejným názvem a typem už existuje a chcete přidat další takové záznam, měli byste existující sadu záznamů upravit.
3.  Pokoušíte se vytvořit záznam ve vrcholu zóny DNS („kořenu“ zóny)? Pokud ano, v rámci konvence DNS se jako název tohoto záznamu používá znak ‘@’. Všimněte si také, že standardy DNS nepovolují záznamy CNAME ve vrcholu zóny.
4.  Vznikl konflikt CNAME?  Standardy DNS nepovolují záznam CNAME se stejným názvem jako záznam jiného typu. Pokud už máte CNAME, vytvoření záznamu jiného typu se stejným názvem se nepovede.  Podobně se vytvoření CNAME nepovede, pokud jeho název odpovídá existujícímu záznamu jiného typu. Konflikt odstraníte odebráním druhého záznamu nebo volbou jiného názvu záznamu.
5.  Dosáhli jste limitu povoleného počtu sad záznamů v zóně DNS? Aktuální počet sad záznamů a maximální počet sad záznamů se zobrazuje na webu Azure Portal ve vlastnostech zóny. Pokud jste dosáhli tohoto limitu, buď odstraňte některé sady záznamů, nebo požádejte podporu Azure o zvýšení limitu sady záznamů pro tuto zónu a pak to zkuste znovu. 


### <a name="recommended-articles"></a>Doporučené články

* [Záznamy a zóny DNS](dns-zones-records.md)
* [Vytvoření zóny DNS](./dns-getstarted-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Nejde mi přeložit záznam DNS

Překlad názvů DNS je vícefázový proces, který může selhat z mnoha důvodů. Následující kroky vám pomůžou zjistit, proč se pro záznam DNS v zóně hostované v Azure DNS nedaří překlad DNS.

1.  Potvrďte, že záznamy DNS jsou v Azure DNS správně nakonfigurované. Zkontrolujte záznamy DNS na webu Azure Portal a ověřte, jestli název zóny, název záznamu a typ záznamu jsou správné.
2.  Zkontrolujte, že se záznamy DNS správně překládají na názvových serverech Azure DNS.
    - Pokud dotazy DNS zpracováváte z vašeho místního počítače, může se stát, že se zobrazují výsledky uložené v mezipaměti, které neodrážejí aktuální stav serverů.  Podnikové sítě navíc často používají proxy servery DNS, které zabraňují směrování dotazů DNS na konkrétní názvové servery.  Pokud se chcete těmto problémům vyhnout, použijte webovou službu překladu názvů, jako je třeba [digwebinterface](https://digwebinterface.com).
    - Nezapomeňte zadat správné názvové servery pro zónu DNS (jsou uvedené na webu Azure Portal).
    - Zkontrolujte správnost názvu DNS (musíte zadat plně kvalifikovaný název včetně názvu zóny) a typu záznamu.
3.  Potvrďte, že název domény DNS byl správně [delegovaný na názvové servery Azure DNS](dns-domain-delegation.md). Existuje [celá řada webů třetích stran, které poskytují ověření delegování DNS](https://www.bing.com/search?q=dns+check+tool). Jde o test delegování *zóny*, takže byste měli zadat název zóny DNS, a ne plně kvalifikovaný název záznamu.
4.  Po dokončení těchto kroků by se záznam DNS už měl překládat správně. K ověření můžete znovu využít [digwebinterface](https://digwebinterface.com) a tentokrát použít výchozí nastavení názvového serveru.


### <a name="recommended-articles"></a>Doporučené články

* [Delegování domény do Azure DNS](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Jak zadat službu a protokol pro záznam SRV?

Azure DNS spravuje záznamy DNS jako sady záznamů. To jsou kolekce záznamů se stejným názvem a typem. U sady záznamů SRV se služba i protokol musí zadat jako součást názvu sady. Ostatní parametry SRV (priorita, váha, port a cíl) se pro jednotlivé záznamy v sadě záznamů zadávají samostatně.

Ukázkové názvy záznamů SRV (služba = sip, protokol = tcp):

- \_sip.\_tcp (vytvoří sadu záznamů ve vrcholu zóny)
- \_sip.\_tcp.sipservice (vytvoří sadu záznamů s názvem sipservice)

### <a name="recommended-articles"></a>Doporučené články

* [Záznamy a zóny DNS](dns-zones-records.md)
* [Vytváření sad záznamů a záznamů DNS pomocí webu Azure Portal](./dns-getstarted-portal.md)
* [Záznamy typu SRV (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Další kroky

* Další informace o [Azure DNS zón a záznamech](dns-zones-records.md)
* Pokud chcete začít používat Azure DNS, přečtěte si, jak [vytvořit ZÓNU DNS](./dns-getstarted-portal.md) a [vytvořit záznamy DNS](./dns-getstarted-portal.md).
* Pokud chcete migrovat existující zónu DNS, přečtěte si, jak [importovat a exportovat soubor zóny DNS](dns-import-export.md).