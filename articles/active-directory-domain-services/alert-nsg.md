---
title: Vyřešit výstrahy skupiny zabezpečení sítě v Azure služba AD DS | Microsoft Docs
description: Přečtěte si, jak řešit a řešit výstrahy konfigurace skupiny zabezpečení sítě pro Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d8f2e77b7225306844cec85363a2971eaac4eebd
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620252"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Známé problémy: výstrahy konfigurace sítě v Azure Active Directory Domain Services

Aby mohly aplikace a služby správně komunikovat se spravovanou doménou Azure Active Directory Domain Services (Azure služba AD DS), musí být pro povolení toku provozu určité síťové porty otevřené. V Azure řídíte tok provozu pomocí skupin zabezpečení sítě. Stav spravované domény Azure služba AD DS zobrazuje výstrahu, pokud nejsou nastavená požadovaná pravidla skupiny zabezpečení sítě.

Tento článek vám pomůže pochopit a vyřešit běžné výstrahy týkající se problémů s konfigurací skupiny zabezpečení sítě.

## <a name="alert-aadds104-network-error"></a>Výstraha AADDS104: Chyba sítě

### <a name="alert-message"></a>Zpráva výstrahy

*Společnost Microsoft se nemůže spojit s řadiči domény pro tuto spravovanou doménu. K tomu může dojít v případě, že skupina zabezpečení sítě (NSG) nakonfigurovaná ve vaší virtuální síti blokuje přístup ke spravované doméně. Dalším možným důvodem je, že existuje trasa definovaná uživatelem, která blokuje příchozí provoz z Internetu.*

Neplatná pravidla skupiny zabezpečení sítě jsou nejběžnější příčinou chyb sítě pro Azure služba AD DS. Skupina zabezpečení sítě pro virtuální síť musí umožňovat přístup ke konkrétním portům a protokolům. Pokud jsou tyto porty blokované, platforma Azure nemůže monitorovat ani aktualizovat spravovanou doménu. To má vliv na synchronizaci mezi adresářem služby Azure AD a službou Azure služba AD DS. Ujistěte se, že jsou otevřené výchozí porty, aby nedošlo k přerušení provozu.

## <a name="default-security-rules"></a>Výchozí pravidla zabezpečení

Pro skupinu zabezpečení sítě pro spravovanou doménu se aplikují následující výchozí pravidla pro příchozí a odchozí zabezpečení. Tato pravidla zachovají Azure služba AD DS zabezpečená a umožňují platformě Azure monitorovat, spravovat a aktualizovat spravovanou doménu.

### <a name="inbound-security-rules"></a>Příchozí pravidla zabezpečení

| Priorita | Název | Port | Protokol | Zdroj | Cíl | Akce |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Všechny | Povolit |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Všechny | Povolit |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Všechny | Povolit |
| 65000    | AllVnetInBound | Všechny | Všechny | VirtualNetwork | VirtualNetwork | Povolit |
| 65001    | AllowAzureLoadBalancerInBound | Všechny | Všechny | AzureLoadBalancer | Všechny | Povolit |
| 65500    | DenyAllInBound | Všechny | Všechny | Všechny | Všechny | Odepřít |

> [!NOTE]
> Je také možné, že máte další pravidlo, které povolí příchozí provoz, pokud [konfigurujete zabezpečený protokol LDAP][configure-ldaps]. Toto dodatečné pravidlo je vyžadováno pro správné komunikace LDAPs.

### <a name="outbound-security-rules"></a>Odchozí pravidla zabezpečení

| Priorita | Název | Port | Protokol | Zdroj | Cíl | Akce |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Všechny | Všechny | VirtualNetwork | VirtualNetwork | Povolit |
| 65001    | AllowAzureLoadBalancerOutBound | Všechny | Všechny |  Všechny | Internet | Povolit |
| 65500    | DenyAllOutBound | Všechny | Všechny | Všechny | Všechny | Odepřít |

>[!NOTE]
> Azure služba AD DS potřebuje neomezený odchozí přístup z virtuální sítě. Nedoporučujeme vytvářet žádná další pravidla, která omezují odchozí přístup k virtuální síti.

## <a name="verify-and-edit-existing-security-rules"></a>Ověřit a upravit existující pravidla zabezpečení

Pokud chcete ověřit stávající pravidla zabezpečení a ujistit se, že jsou otevřené výchozí porty, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **skupiny zabezpečení sítě**.
1. Vyberte skupinu zabezpečení sítě přidruženou k vaší spravované doméně, například *AADDS-contoso.com-NSG*.
1. Na stránce **Přehled** jsou uvedena existující pravidla pro příchozí a odchozí zabezpečení.

    Zkontrolujte pravidla příchozích a odchozích připojení a porovnejte je se seznamem požadovaných pravidel v předchozí části. V případě potřeby vyberte a odstraňte všechna vlastní pravidla, která blokují požadovaný provoz. Pokud chybí některá z požadovaných pravidel, přidejte pravidlo do další části.

    Po přidání nebo odstranění pravidel, která povolí požadovaný provoz, se stav spravované domény automaticky aktualizuje během dvou hodin a výstraha se odstraní.

### <a name="add-a-security-rule"></a>Přidání pravidla zabezpečení

Chcete-li přidat chybějící pravidlo zabezpečení, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **skupiny zabezpečení sítě**.
1. Vyberte skupinu zabezpečení sítě přidruženou k vaší spravované doméně, například *AADDS-contoso.com-NSG*.
1. V části **Nastavení** na levém panelu klikněte na *příchozí pravidla zabezpečení* nebo na *odchozí pravidla zabezpečení* v závislosti na tom, které pravidlo potřebujete přidat.
1. Vyberte **Přidat** a pak vytvořte požadované pravidlo na základě portu, protokolu, směru atd. Až budete připraveni, vyberte **OK**.

Přidání a zobrazení pravidla zabezpečení v seznamu bude chvíli trvat.

## <a name="next-steps"></a>Další kroky

Pokud stále máte problémy, [otevřete žádost o podporu Azure][azure-support] , kde najdete další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
