---
title: Funkce služby Azure AD Connect sync a konfigurace | Dokumentace Microsoftu
description: Popisuje funkce na straně služby pro službu synchronizace Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7b3da5b2340b6bd4dd49dd6f8278f2fced477bc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190717"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funkce služby synchronizace Azure AD Connect
Funkce synchronizace služby Azure AD Connect má dvě součásti:

* Místní součást s názvem **synchronizace Azure AD Connect**, označované také jako **synchronizační modul**.
* Služby, které se nacházejí ve službě Azure AD označované také jako **služby synchronizace Azure AD Connect**

Toto téma vysvětluje, jak tyto funkce **služby synchronizace Azure AD Connect** práce a jak pomocí prostředí Windows PowerShell nakonfigurovat.

Tato nastavení jsou konfigurována pomocí [modulu Azure Active Directory pro Windows PowerShell](https://aka.ms/aadposh). Stáhněte si a nainstalujte odděleně od Azure AD Connect. Rutiny uvedené v tomto tématu byly zavedeny v [vydání březen 2016 (build 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Pokud používáte rutiny popsané v tomto tématu nebo záměrně neprodukují stejný výsledek, ujistěte se, že běží nejnovější verze.

Pokud chcete zobrazit konfiguraci v adresáři služby Azure AD, spusťte `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures result](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Mnohé z těchto nastavení můžete změnit jenom pomocí služby Azure AD Connect.

Následující nastavení lze nakonfigurovat podle `Set-MsolDirSyncFeature`:

| DirSyncFeature | Poznámka |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Umožňuje objekty spojit userPrincipalName kromě primární adresu SMTP. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Umožňuje synchronizační modul aktualizovat atribut userPrincipalName pro spravované/licencovaní uživatelé (jiné než federované). |

Po povolení funkce nejde znovu zakázat.

> [!NOTE]
> Z 24. srpna 2016 funkci *odolnost vůči duplicitě atributů* je ve výchozím nastavení povolené pro nový Azure AD adresáře. Tato funkce se také zavádí a povolí na adresáře vytvořené před tímto datem. Zobrazí se oznámení e-mailem a adresáři se získat povolení této funkce.
> 
> 

Následující nastavení jsou nakonfigurovány pomocí služby Azure AD Connect a nelze ji změnit pomocí `Set-MsolDirSyncFeature`:

| DirSyncFeature | Poznámka |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Povolení zpětného zápisu zařízení](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Synchronizace Azure AD Connect: Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Umožňuje atribut, který má být umístěn do karantény, když je duplikátem jiného objektu spíš než celý objekt selhání během exportu. |
| Synchronizace hodnoty hash hesel |[Implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect](how-to-connect-password-hash-synchronization.md) |
|Předávací ověřování|[Přihlašování uživatelů s využitím předávacího ověřování služby Azure Active Directory](how-to-connect-pta.md)|
| UnifiedGroupWriteback |[Ve verzi Preview: Zpětný zápis skupin](how-to-connect-preview.md#group-writeback) |
| UserWriteback |Není aktuálně podporováno. |

## <a name="duplicate-attribute-resiliency"></a>Odolnost vůči duplicitě atributů
Místo selhání zřizování objekty s duplicitní názvy UPN / proxyAddresses, duplicitní atribut "v karanténě" a je dočasná hodnota přiřazena. Po vyřešení konfliktu dočasné hlavní název uživatele se automaticky změní na správnou hodnotu. Další podrobnosti najdete v tématu [Identity synchronizace a odolnost duplicitních atributů](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName obnovitelně shody
Pokud je tato funkce povolena, konfigurace soft-match je povolená pro hlavní název uživatele kromě [primární adresu SMTP](https://support.microsoft.com/kb/2641663), což je vždy povolena. Konfigurace soft-match se používá tak, aby odpovídala stávající cloudové uživatele ve službě Azure AD pomocí místních uživatelů.

Pokud potřebujete shoda místní účty služby AD s existující účty vytvořené v cloudu a nepoužíváte Exchange Online, bude tato funkce je užitečná. V tomto scénáři obvykle není nutné z důvodu nastavit atribut SMTP v cloudu.

Tato funkce je na ve výchozím nastavení pro nově vytvořené adresářů služby Azure AD. Můžete zobrazit, pokud je tato funkce povolena pro vás spuštěním:  

```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Pokud tato funkce není povolena pro váš adresář Azure AD, můžete ji povolit spuštěním:  

```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synchronizace aktualizací userPrincipalName
V minulosti aktualizace pomocí služby sync z místní atribut UserPrincipalName je zablokovaný, pokud jsou splněny obě tyto podmínky:

* Jde o spravovaného uživatele (jiné než federované).
* Uživateli nebyla přiřazena licence.

Další podrobnosti najdete v tématu [uživatelská jména v Office 365, Azure nebo Intune se neshodují s místními UPN nebo alternativní přihlašovací ID](https://support.microsoft.com/kb/2523192).

Zapnutím této funkce povolíte synchronizační modul aktualizovat userPrincipalName, pokud je změněný v místním a pomocí synchronizace hodnot hash hesel. Pokud používáte federace, tato funkce není podporována.

Tato funkce je na ve výchozím nastavení pro nově vytvořené adresářů služby Azure AD. Můžete zobrazit, pokud je tato funkce povolena pro vás spuštěním:  

```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Pokud tato funkce není povolena pro váš adresář Azure AD, můžete ji povolit spuštěním:  

```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Po povolení této funkce, zůstanou existující hodnoty userPrincipalName jako-je. Při příští změně userPrincipalName atribut místní normální rozdílová synchronizace na uživatele se aktualizuje hlavní název uživatele.  

## <a name="see-also"></a>Další informace najdete v tématech
* [Synchronizace služby Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).

