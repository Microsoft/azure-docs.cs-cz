---
title: 'Azure Active Directory Domain Services: Řešení potíží se skupinami zabezpečení sítě | Microsoft Docs'
description: Řešení potíží s konfigurací skupiny zabezpečení sítě pro Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 450ee5635b378ed7c4d4e4bedc1c4245f6b52d70
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "70743446"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Řešení potíží s neplatnou konfigurací sítě pro spravovanou doménu
Tento článek vám pomůže vyřešit problémy s konfigurací související se sítí a vyřešit chyby v síti, které mají za následek následující zprávu výstrahy:

## <a name="alert-aadds104-network-error"></a>AADDS104 výstrahy: Chyba sítě
**Zpráva upozornění:** *Společnost Microsoft se nemůže spojit s řadiči domény pro tuto spravovanou doménu. K tomu může dojít v případě, že skupina zabezpečení sítě (NSG) nakonfigurovaná ve vaší virtuální síti blokuje přístup ke spravované doméně. Dalším možným důvodem je, že existuje trasa definovaná uživatelem, která blokuje příchozí provoz z Internetu.*

Mezi nejběžnější příčiny chyb sítě pro Azure AD Domain Services jsou neplatné konfigurace NSG. Skupina zabezpečení sítě (NSG) nakonfigurovaná pro vaši virtuální síť musí umožňovat přístup ke [konkrétním portům](network-considerations.md#network-security-groups-and-required-ports). Pokud jsou tyto porty blokované, Microsoft nemůže monitorovat ani aktualizovat spravovanou doménu. Kromě toho má vliv na synchronizaci mezi adresářem služby Azure AD a vaší spravovanou doménou. Při vytváření NSG zachovejte tyto porty otevřené, aby nedošlo k přerušení provozu.

### <a name="checking-your-nsg-for-compliance"></a>Kontroluje se NSG dodržování předpisů.

1. Přejít na stránku [skupiny zabezpečení sítě](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) v Azure Portal
2. V tabulce vyberte NSG přidružené k podsíti, ve které je povolená vaše spravovaná doména.
3. V části **Nastavení** na levém panelu klikněte na **příchozí pravidla zabezpečení** .
4. Zkontrolujte pravidla, která jsou zavedena, a určete, která pravidla blokují přístup k [těmto portům](network-considerations.md#network-security-groups-and-required-ports) .
5. Upravte NSG tak, aby se zajistilo dodržování předpisů tím, že odstraníte pravidlo, přidáte pravidlo nebo vytvoříte nový NSG úplně. Postup [Přidání pravidla](#add-a-rule-to-a-network-security-group-using-the-azure-portal) nebo vytvoření nového kompatibilního NSG najdete níže.

## <a name="sample-nsg"></a>Ukázka NSG
Následující tabulka popisuje ukázkovou NSGi, která by zachovala zabezpečení spravované domény a současně umožňuje Microsoftu monitorovat, spravovat a aktualizovat informace.

![Ukázka NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services vyžaduje neomezený odchozí přístup z virtuální sítě. Nedoporučujeme vytvářet žádná další pravidla NSG, která omezují odchozí přístup k virtuální síti.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Přidat pravidlo do skupiny zabezpečení sítě pomocí Azure Portal
Pokud nechcete používat PowerShell, můžete do skupin zabezpečení sítě ručně přidat jednoduchá pravidla pomocí Azure Portal. Pokud chcete ve skupině zabezpečení sítě vytvořit pravidla, proveďte následující kroky:

1. Přejděte na stránku [skupiny zabezpečení sítě](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) v Azure Portal.
2. V tabulce vyberte NSG přidružené k podsíti, ve které je povolená vaše spravovaná doména.
3. V části **Nastavení** na levém panelu klikněte buď na **pravidla zabezpečení příchozí** , nebo na **odchozí pravidla zabezpečení**.
4. Vytvořte pravidlo kliknutím na **Přidat** a vyplněním informací. Klikněte na **OK**.
5. Ověřte, že se pravidlo vytvořilo tak, že ho vyhledáte v tabulce Rules.


## <a name="need-help"></a>Potřebujete pomoct?
Kontaktujte tým Azure Active Directory Domain Services produktů, abyste mohli [sdílet zpětnou vazbu nebo podporu](contact-us.md).
