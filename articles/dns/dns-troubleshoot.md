---
title: Troubleshooting guide - Azure DNS
description: In this learning path, get started troubleshooting common issues with Azure DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: allensu
ms.openlocfilehash: b5fedba7b739c07a37f3aabf75ddd8ca465ba73b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210942"
---
# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS troubleshooting guide

This article provides troubleshooting information for common Azure DNS questions.

If these steps don't resolve your issue, you can also search for or post your issue on our [community support forum on MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Or, you can open an Azure support request.


## <a name="i-cant-create-a-dns-zone"></a>I can't create a DNS zone

Při řešení běžných problémů zkuste použít jeden nebo několik následujících kroků:

1.  Review the Azure DNS audit logs to determine the failure reason.
2.  Název každé zóny DNS musí být v rámci dané skupiny prostředků jedinečný. That is, two DNS zones with the same name can't share a resource group. Zkuste použít jiný název zóny nebo jinou skupinu prostředků.
3.  Může se zobrazit chyba typu Dosáhli nebo přesáhli jste maximální počet zón v předplatném {id předplatného}. Použijte jiné předplatné Azure, odstraňte některé zóny nebo kontaktujte podporu Azure se žádostí o zvýšení limitu vašeho předplatného.
4.  Může se zobrazit chyba typu Zóna {název zóny} není k dispozici. Tato chyba znamená, že Azure DNS se pro tuto zónu DNS nepodařilo přidělit názvové servery. Zkuste použít jiný název zóny. Or, if you are the domain name owner you can contact Azure support to allocate name servers for you.


### <a name="recommended-articles"></a>Recommended articles

* [Záznamy a zóny DNS](dns-zones-records.md)
* [Vytvoření zóny DNS](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Nejde mi vytvořit záznam DNS

Při řešení běžných problémů zkuste použít jeden nebo několik následujících kroků:

1.  Review the Azure DNS audit logs to determine the failure reason.
2.  Už tato sada záznamů existuje?  Azure DNS spravuje záznamy pomocí *sad* záznamů. To jsou kolekce záznamů se stejným názvem a typem. Pokud záznam se stejným názvem a typem už existuje a chcete přidat další takové záznam, měli byste existující sadu záznamů upravit.
3.  Pokoušíte se vytvořit záznam ve vrcholu zóny DNS („kořenu“ zóny)? Pokud ano, v rámci konvence DNS se jako název tohoto záznamu používá znak ‘@’. Also note that the DNS standards don't permit CNAME records at the zone apex.
4.  Vznikl konflikt CNAME?  The DNS standards don't allow a CNAME record with the same name as a record of any other type. Pokud už máte CNAME, vytvoření záznamu jiného typu se stejným názvem se nepovede.  Podobně se vytvoření CNAME nepovede, pokud jeho název odpovídá existujícímu záznamu jiného typu. Konflikt odstraníte odebráním druhého záznamu nebo volbou jiného názvu záznamu.
5.  Dosáhli jste limitu povoleného počtu sad záznamů v zóně DNS? Aktuální počet sad záznamů a maximální počet sad záznamů se zobrazuje na webu Azure Portal ve vlastnostech zóny. If you've reached this limit, then either delete some record sets or contact Azure Support to raise your record set limit for this zone, then try again. 


### <a name="recommended-articles"></a>Recommended articles

* [Záznamy a zóny DNS](dns-zones-records.md)
* [Vytvoření zóny DNS](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Nejde mi přeložit záznam DNS

Překlad názvů DNS je vícefázový proces, který může selhat z mnoha důvodů. Následující kroky vám pomůžou zjistit, proč se pro záznam DNS v zóně hostované v Azure DNS nedaří překlad DNS.

1.  Potvrďte, že záznamy DNS jsou v Azure DNS správně nakonfigurované. Zkontrolujte záznamy DNS na webu Azure Portal a ověřte, jestli název zóny, název záznamu a typ záznamu jsou správné.
2.  Zkontrolujte, že se záznamy DNS správně překládají na názvových serverech Azure DNS.
    - Pokud dotazy DNS zpracováváte z vašeho místního počítače, může se stát, že se zobrazují výsledky uložené v mezipaměti, které neodrážejí aktuální stav serverů.  Podnikové sítě navíc často používají proxy servery DNS, které zabraňují směrování dotazů DNS na konkrétní názvové servery.  Pokud se chcete těmto problémům vyhnout, použijte webovou službu překladu názvů, jako je třeba [digwebinterface](https://digwebinterface.com).
    - Nezapomeňte zadat správné názvové servery pro zónu DNS (jsou uvedené na webu Azure Portal).
    - Zkontrolujte správnost názvu DNS (musíte zadat plně kvalifikovaný název včetně názvu zóny) a typu záznamu.
3.  Potvrďte, že název domény DNS byl správně [delegovaný na názvové servery Azure DNS](dns-domain-delegation.md). Existuje [celá řada webů třetích stran, které poskytují ověření delegování DNS](https://www.bing.com/search?q=dns+check+tool). Jde o test delegování *zóny*, takže byste měli zadat název zóny DNS, a ne plně kvalifikovaný název záznamu.
4.  Po dokončení těchto kroků by se záznam DNS už měl překládat správně. K ověření můžete znovu využít [digwebinterface](https://digwebinterface.com) a tentokrát použít výchozí nastavení názvového serveru.


### <a name="recommended-articles"></a>Recommended articles

* [Delegování domény do Azure DNS](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Jak zadat službu a protokol pro záznam SRV?

Azure DNS spravuje záznamy DNS jako sady záznamů. To jsou kolekce záznamů se stejným názvem a typem. U sady záznamů SRV se služba i protokol musí zadat jako součást názvu sady. Ostatní parametry SRV (priorita, váha, port a cíl) se pro jednotlivé záznamy v sadě záznamů zadávají samostatně.

Ukázkové názvy záznamů SRV (služba = sip, protokol = tcp):

- \_sip.\_tcp (vytvoří sadu záznamů ve vrcholu zóny)
- \_sip.\_tcp.sipservice (vytvoří sadu záznamů s názvem sipservice)

### <a name="recommended-articles"></a>Recommended articles

* [Záznamy a zóny DNS](dns-zones-records.md)
* [Vytváření sad záznamů a záznamů DNS pomocí webu Azure Portal](dns-getstarted-create-recordset-portal.md)
* [Záznamy typu SRV (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Další kroky

* Learn about [Azure DNS zones and records](dns-zones-records.md)
* To start using Azure DNS, learn how to [create a DNS zone](dns-getstarted-create-dnszone-portal.md) and [create DNS records](dns-getstarted-create-recordset-portal.md).
* To migrate an existing DNS zone, learn how to [import and export a DNS zone file](dns-import-export.md).

