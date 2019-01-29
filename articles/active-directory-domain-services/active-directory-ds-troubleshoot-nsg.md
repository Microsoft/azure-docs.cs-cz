---
title: 'Azure Active Directory Domain Services: Konfigurace řešení potíží s skupiny zabezpečení sítě | Dokumentace Microsoftu'
description: Řešení potíží s konfigurací NSG pro službu Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: 2f66c6956c803979ee6717f7327379c7bc9e97bb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181890"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Řešení potíží s neplatnou konfiguraci sítě pro vaši spravovanou doménu
Tento článek pomáhá odstraňovat potíže a řešit chyby související se sítí konfigurace, které za následek následující upozornění:

## <a name="alert-aadds104-network-error"></a>Upozornění AADDS104: Chyba sítě
**Zpráva s výstrahou:** *Společnost Microsoft se nám kontaktovat řadiče této spravované doméně. To může stát v případě skupinu zabezpečení sítě (NSG) nakonfigurovaná ve vaší virtuální síti blokuje přístup ke spravované doméně. Dalším důvodem je, pokud trasy definované uživatelem, která blokuje příchozí provoz z Internetu.*

Neplatná konfigurace skupiny zabezpečení sítě jsou nejčastější příčina chyby sítě pro službu Azure AD Domain Services. Skupiny zabezpečení sítě (NSG) nakonfigurovaná pro virtuální síť musí umožňovat přístup k [určité porty](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Pokud tyto porty jsou blokované, Microsoft nemůže monitorovat nebo aktualizovat vaší spravované domény. Kromě toho má vliv synchronizace adresáře služby Azure AD a vaší spravované domény. Při vytváření vaší skupiny zabezpečení sítě, nechte tyto porty otevřené zabránili přerušení služby.

### <a name="checking-your-nsg-for-compliance"></a>Kontroluje se skupina NSG pro dodržování předpisů

1. Přejděte [skupiny zabezpečení sítě](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) stránky na webu Azure Portal
2. Z tabulky zvolte skupinu zabezpečení sítě přidružená k podsíti, ve kterém je povolena vaší spravované domény.
3. V části **nastavení** na levém panelu klikněte na tlačítko **příchozí pravidla zabezpečení**
4. Zkontrolujte pravidla na místě a určit, která pravidla blokují přístup k [tyto porty](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)
5. Upravte skupiny NSG k zajištění dodržování předpisů buď odstraňuje se pravidlo, přidání pravidla nebo zcela vytvořením nové skupiny zabezpečení sítě. Kroky pro [přidat pravidlo](#add-a-rule-to-a-network-security-group-using-the-azure-portal) nebo [vytvořit nové, který vyhovuje NSG](#create-a-nsg-for-azure-ad-domain-services-using-powershell) jsou níže

## <a name="sample-nsg"></a>Ukázková skupina zabezpečení sítě
Následující tabulka znázorňuje ukázku NSG, která by zabezpečení vaší spravované domény zároveň umožní Microsoftu monitorovat, spravovat a aktualizovat informace.

![Ukázková skupina zabezpečení sítě](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services vyžaduje neomezený odchozího přístupu z virtuální sítě. Doporučujeme nevytvářet jakékoli další pravidlo NSG, která omezuje přístup pro odchozí připojení pro virtuální síť.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Přidejte pravidlo skupiny zabezpečení sítě pomocí webu Azure portal
Pokud nechcete, jak pomocí prostředí PowerShell, můžete ručně přidat jednoho pravidla do skupin zabezpečení sítě pomocí webu Azure portal. Vytváření pravidel ve skupině zabezpečení sítě, proveďte následující kroky:

1. Přejděte [skupiny zabezpečení sítě](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) stránky na webu Azure Portal.
2. Z tabulky zvolte skupinu zabezpečení sítě přidružená k podsíti, ve kterém je povolena vaší spravované domény.
3. V části **nastavení** na levém panelu klikněte na možnost **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení**.
4. Vytvořit pravidlo kliknutím **přidat** a vyplníte informace. Klikněte na **OK**.
5. Ověřte, že vaše pravidlo bylo vytvořené ve vyhledávání v tabulce pravidel.


## <a name="need-help"></a>Potřebujete pomoc?
Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
