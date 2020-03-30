---
title: Funkce a konfigurace synchronizační služby Azure AD Connect | Dokumenty společnosti Microsoft
description: Popisuje funkce na straně služby pro synchronizační službu Azure AD Connect.
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
ms.openlocfilehash: 5486a8d8bd4c295f49e0ab847daf45d0fcab47ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300532"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funkce synchronizační služby Azure AD Connect

Funkce synchronizace služby Azure AD Connect má dvě součásti:

* Místní součást s názvem **Synchronizace Azure AD Connect**, označovaná také **jako synchronizační modul**.
* Služba s bydlištěm ve službě Azure AD označovaná také jako **synchronizační služba Azure AD Connect**

Toto téma vysvětluje, jak fungují následující funkce **synchronizační služby Azure AD Connect** a jak je můžete nakonfigurovat pomocí prostředí Windows PowerShell.

Tato nastavení nakonfiguruje [modul Azure Active Directory Module pro prostředí Windows PowerShell](https://aka.ms/aadposh). Stáhněte si a nainstalujte ji odděleně od Azure AD Connect. Rutiny zdokumentované v tomto tématu byly představeny v [březnové verzi 2016 (build 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Pokud nemáte rutiny zdokumentované v tomto tématu nebo nevytvářejí stejný výsledek, ujistěte se, že spustíte nejnovější verzi.

Konfiguraci v adresáři Azure AD `Get-MsolDirSyncFeatures`zobrazíte, spusťte .  
![Get-MsolDirSyncVýsledek](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Mnoho z těchto nastavení lze změnit pouze pomocí Azure AD Connect.

Následující nastavení lze konfigurovat `Set-MsolDirSyncFeature`pomocí :

| Funkce DirSyncFeature | Poznámka |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Umožňuje objektům připojit se k userPrincipalName kromě primární adresy SMTP. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Umožňuje synchronizačnímu modulu aktualizovat atribut userPrincipalName pro spravované/licencované (nefederované) uživatele. |

Po povolení funkce ji nelze znovu zakázat.

> [!NOTE]
> srpna 24, 2016 funkce *Duplicitní atribut odolnost je* povolena ve výchozím nastavení pro nové adresáře Azure AD. Tato funkce bude také zavedena a povolena v adresářích vytvořených před tímto datem. Obdržíte e-mailové oznámení, když se váš adresář chystá tuto funkci aktivovat.
> 
> 

Následující nastavení jsou nakonfigurována službou Azure `Set-MsolDirSyncFeature`AD Connect a nelze je změnit pomocí :

| Funkce DirSyncFeature | Poznámka |
| --- | --- |
| Zpětné zápisu zařízení |[Azure AD Connect: Povolení zpětného zápisu zařízení](how-to-connect-device-writeback.md) |
| Rozšíření adresářů |[Synchronizace služby Azure AD Connect: Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md) |
| [Duplicitní proxyaddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Umožňuje atribut uřadit do karantény, pokud je duplicitní jiného objektu, nikoli selhání celého objektu během exportu. |
| Synchronizace hodnoty hash hesel |[Implementace synchronizace hash hesel se synchronizací Azure AD Connect](how-to-connect-password-hash-synchronization.md) |
|Předávací ověřování|[Přihlašování uživatelů s využitím předávacího ověřování služby Azure Active Directory](how-to-connect-pta.md)|
| UnifiedGroupWriteback |[Náhled: Skupinový zpětný zápis](how-to-connect-preview.md#group-writeback) |
| Zpětné nastavení uživatele |Momentálně není podporováno. |

## <a name="duplicate-attribute-resiliency"></a>Odolnost proti chybám duplicitního atributu

Namísto selhání zřídit objekty s duplicitní UPN / proxyAdresy, duplicitní atribut je "v karanténě" a je přiřazena dočasná hodnota. Po vyřešení konfliktu se dočasný hlavní upn automaticky změní na správnou hodnotu. Další podrobnosti naleznete v [tématu Synchronizace identit a odolnost proti duplicitnímu atributu](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Měkká shoda UserPrincipalName

Pokud je tato funkce povolena, je pro hlavní upn povolena také pro hlavní síť pro [obnovitelné](https://support.microsoft.com/kb/2641663)položky , která je vždy povolena. Měkká shoda se používá k přizpůsobení stávajících uživatelů cloudu ve službě Azure AD s místními uživateli.

Pokud potřebujete porovnat místní účty služby AD s existujícími účty vytvořenými v cloudu a nepoužíváte Exchange Online, je tato funkce užitečná. V tomto scénáři obecně nemáte důvod k nastavení atributu SMTP v cloudu.

Tato funkce je ve výchozím nastavení pro nově vytvořené adresáře Azure AD. Tuto funkci můžete zjistit spuštěním:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Pokud tato funkce není povolena pro váš adresář Azure AD, můžete ji povolit spuštěním:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synchronizace aktualizací userPrincipalName

Historicky byly blokovány aktualizace atributu UserPrincipalName pomocí služby synchronizace z místního prostředí, pokud nejsou splněny obě tyto podmínky:

* Uživatel je spravován (nefederovaný).
* Uživateli nebyla přiřazena licence.

Další podrobnosti najdete [v tématu Uživatelská jména v Office 365, Azure nebo Intune neodpovídají místní UPN nebo alternativní přihlašovací ID](https://support.microsoft.com/kb/2523192).

Povolení této funkce umožňuje synchronizačnímu modulu aktualizovat userPrincipalName při změně místně a použití synchronizace hash hesla nebo předávacího ověřování.

Tato funkce je ve výchozím nastavení pro nově vytvořené adresáře Azure AD. Tuto funkci můžete zjistit spuštěním:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Pokud tato funkce není povolena pro váš adresář Azure AD, můžete ji povolit spuštěním:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Po povolení této funkce zůstanou existující hodnoty userPrincipalName tak, jak jsou. Při další změně atributu userPrincipalName v místním prostředí aktualizuje normální synchronizace delta u uživatelů hlavní název uživatele.  

## <a name="see-also"></a>Viz také

* [Synchronizace Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).
