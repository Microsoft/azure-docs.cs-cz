---
title: Řešení výstrah skupiny zabezpečení sítě ve službě Azure AD DS | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit potíže a řešení výstrah konfigurace skupiny zabezpečení sítě pro službu Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258003"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Známé problémy: Výstrahy konfigurace sítě ve službě Azure Active Directory Domain Services

Aby aplikace a služby mohly správně komunikovat se službou Azure Active Directory Domain Services (Azure AD DS), musí být otevřené konkrétní síťové porty, aby bylo možné povolit tok provozu. V Azure řídíte tok provozu pomocí skupin zabezpečení sítě. Stav spravované domény Azure AD DS zobrazuje výstrahu, pokud nejsou zavedena požadovaná pravidla skupiny zabezpečení sítě.

Tento článek vám pomůže pochopit a vyřešit běžné výstrahy pro problémy s konfigurací skupiny zabezpečení sítě.

## <a name="alert-aadds104-network-error"></a>Výstraha AADDS104: Chyba sítě

### <a name="alert-message"></a>Výstražná zpráva

*Společnost Microsoft nemůže kontaktovat řadiče domény pro tuto spravovanou doménu. K tomu může dojít, pokud skupina zabezpečení sítě (NSG) nakonfigurovaná ve vaší virtuální síti blokuje přístup ke spravované doméně. Dalším možným důvodem je, pokud existuje uživatelem definovaná trasa, která blokuje příchozí provoz z Internetu.*

Neplatná pravidla skupiny zabezpečení sítě jsou nejčastější příčinou chyb sítě pro Azure AD DS. Skupina zabezpečení sítě pro virtuální síť musí umožňovat přístup k určitým portům a protokolům. Pokud jsou tyto porty blokované, platforma Azure nemůže monitorovat nebo aktualizovat spravovanou doménu. Synchronizace mezi adresářem Azure AD a spravovanou doménou Azure AD DS je také ovlivněna. Ujistěte se, že máte otevřené výchozí porty, abyste zabránili přerušení provozu.

## <a name="default-security-rules"></a>Výchozí pravidla zabezpečení

Následující výchozí příchozí a odchozí pravidla zabezpečení se použijí na skupinu zabezpečení sítě pro spravovanou doménu Azure AD DS. Tato pravidla udržují Azure AD DS zabezpečené a umožňují platformě Azure monitorovat, spravovat a aktualizovat spravovanou doménu. Můžete mít také další pravidlo, které umožňuje příchozí přenosy, pokud [nakonfigurujete zabezpečené LDAP][configure-ldaps].

### <a name="inbound-security-rules"></a>Příchozí pravidla zabezpečení

| Priorita | Name (Název) | Port | Protocol (Protokol) | Zdroj | Cíl | Akce |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | Služby AzureActiveDirectoryDomainServices | Všechny | Povolit |
| 201      | Povolit rd | 3389 | TCP | CorpNetSaw | Všechny | Povolit |
| 301      | AllowPSRemoting | 5986| TCP | Služby AzureActiveDirectoryDomainServices | Všechny | Povolit |
| 65000    | AllVnetInBound | Všechny | Všechny | VirtualNetwork | VirtualNetwork | Povolit |
| 65001    | AllowAzureLoadBalancerInBound | Všechny | Všechny | AzureLoadBalancer | Všechny | Povolit |
| 65500    | DenyAllInBound | Všechny | Všechny | Všechny | Všechny | Odepřít |

### <a name="outbound-security-rules"></a>Odchozí pravidla zabezpečení

| Priorita | Name (Název) | Port | Protocol (Protokol) | Zdroj | Cíl | Akce |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Všechny | Všechny | VirtualNetwork | VirtualNetwork | Povolit |
| 65001    | AllowAzureLoadBalancerOutBoundOutBound AllowAzure | Všechny | Všechny |  Všechny | Internet | Povolit |
| 65500    | DenyAllOutBound | Všechny | Všechny | Všechny | Všechny | Odepřít |

>[!NOTE]
> Azure AD DS potřebuje neomezený odchozí přístup z virtuální sítě. Nedoporučujeme vytvářet další pravidla, která omezují odchozí přístup pro virtuální síť.

## <a name="verify-and-edit-existing-security-rules"></a>Ověření a úprava existujících pravidel zabezpečení

Chcete-li ověřit existující pravidla zabezpečení a zajistit, aby byly výchozí porty otevřené, proveďte následující kroky:

1. Na webu Azure Portal vyhledejte a vyberte **skupiny zabezpečení sítě**.
1. Zvolte skupinu zabezpečení sítě přidruženou ke spravované doméně, *například AADDS-contoso.com-NSG*.
1. Na stránce **Přehled** se zobrazí existující příchozí a odchozí pravidla zabezpečení.

    Zkontrolujte příchozí a odchozí pravidla a porovnejte seznam požadovaných pravidel v předchozí části. V případě potřeby vyberte a odstraňte všechna vlastní pravidla, která blokují požadovaný provoz. Pokud některé z požadovaných pravidel chybí, přidejte pravidlo v další části.

    Po přidání nebo odstranění pravidel povolit požadovaný provoz, Azure AD DS spravované domény stav automaticky aktualizuje sám do dvou hodin a odebere výstrahu.

### <a name="add-a-security-rule"></a>Přidání pravidla zabezpečení

Chcete-li přidat chybějící pravidlo zabezpečení, proveďte následující kroky:

1. Na webu Azure Portal vyhledejte a vyberte **skupiny zabezpečení sítě**.
1. Zvolte skupinu zabezpečení sítě přidruženou ke spravované doméně, *například AADDS-contoso.com-NSG*.
1. V části **Nastavení** na levém panelu klikněte na *Příchozí pravidla zabezpečení* nebo Odchozí bezpečnostní *pravidla* podle toho, které pravidlo je třeba přidat.
1. Vyberte **Přidat**, pak vytvořte požadované pravidlo na základě portu, protokolu, směru atd. Až budete připraveni, vyberte **OK**.

Trvá několik okamžiků, než se pravidlo zabezpečení přidá a zobrazí v seznamu.

## <a name="next-steps"></a>Další kroky

Pokud máte stále problémy, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
