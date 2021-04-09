---
title: Co je Azure AD Connect a Connect Health. | Dokumentace Microsoftu
description: Přečtěte si o nástrojích, které se používají k synchronizaci a monitorování místního prostředí s využitím Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8e1af1848405441088796d2e3b42e7b52eedba8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98065112"
---
# <a name="what-is-azure-ad-connect"></a>Co je Azure AD Connect?

Azure AD Connect je nástroj od Microsoftu, jehož účelem je splnit a zajistit cíle hybridní identity.  Má následující funkce:
     
- [Synchronizace hodnot hash hesel](whatis-phs.md) – Metoda přihlašování, která synchronizuje hash hesla místních uživatelů služby AD pomocí Azure AD.
- [Předávací ověřování](how-to-connect-pta.md) – Metoda přihlašování, která umožňuje uživatelům používat stejné heslo místně a v cloudu, ale nevyžaduje další infrastrukturu federovaného prostředí.
- [Integrace federace](how-to-connect-fed-whatis.md) – federace je volitelná součást Azure AD Connect a je možné ji použít ke konfiguraci hybridního prostředí pomocí místní infrastruktury AD FS. Poskytuje taky AD FS možnosti správy, jako je například obnovení certifikátu a další AD FS nasazení serveru.
- [Synchronizace](how-to-connect-sync-whatis.md) – zodpovídá za vytváření uživatelů, skupin a dalších objektů.  A zároveň se ujistěte, že informace o identitě místních uživatelů a skupin odpovídají cloudu.  Tato synchronizace zahrnuje taky hodnoty hash hesel.
- [Sledování stavu](whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) – Azure AD Connect Health může poskytovat robustní monitorování a poskytnout centrální umístění v Azure Portal k zobrazení této aktivity. 


![Co je služba Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Co je Azure AD Connect Health?

Služba Azure Active Directory (Azure AD) Connect Health poskytuje robustní monitorování místní infrastruktury identity. Umožňuje udržovat spolehlivé připojení k Microsoft 365 a službám Microsoft Online Services.  Tato spolehlivost se dosahuje tím, že poskytuje možnosti monitorování pro klíčové komponenty identity. Také usnadňuje přístup klíčových datových bodů k těmto součástem.

Další informace najdete v článku [Portál služby Azure AD Connect Health](https://aka.ms/aadconnecthealth). Pomocí portálu Azure AD Connect Health můžete zobrazit výstrahy, monitorování výkonu, analýzu využití a další informace. Azure AD Connect Health umožňuje mít na jednom místě a v jediném přehledu informace o stavu klíčových komponent identity.

![Co je Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Proč používat Azure AD Connect?
Integrace místních adresářů se službou Azure AD zvyšuje produktivitu uživatelů tím, že jim poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Uživatelé a organizace můžou využít tyto možnosti:

* Uživatelé můžou použít jedinou identitu pro přístup k místním aplikacím a cloudovým službám, jako je Microsoft 365.
* Získáváte jeden nástroj, který umožňuje snadné nasazení pro synchronizaci a přihlašování.
* Přináší nejnovější schopnosti pro vaše scénáře. Azure AD Connect nahrazuje starší verze nástrojů pro integraci identity, jako je DirSync a Azure AD Sync. Další informace najdete v tématu [porovnání nástrojů pro integraci adresářů hybridní identity](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Proč používat službu Azure AD Connect Health?
Při práci s Azure AD jsou vaši uživatelé produktivnější, protože máme společnou identitu pro přístup k cloudovým i místním prostředkům. Zajištění spolehlivého prostředí, aby uživatelé měli přístup k těmto prostředkům, se stává výzvou.  Azure AD Connect Health pomáhá monitorovat a získávat přehledy o místní infrastruktuře identity, takže zajišťuje spolehlivost tohoto prostředí. Stačí jednoduše nainstalovat agenta na každý z vašich místních serverů identity.

Azure AD Connect Health pro službu AD FS podporuje službu AD FS 2.0 v systémech Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Podporuje také monitorování proxy serveru služby AD FS a proxy serverů webových aplikací, které poskytují ověřování pro přístup z extranetu. Díky snadné a rychlé instalaci agenta služby Health poskytuje Azure AD Connect Health pro službu AD FS sadu klíčových funkcí.

Klíčové výhody a osvědčené postupy:

|Klíčové výhody|Osvědčené postupy|
|-----|-----|
|Posílené zabezpečení|[Trendy uzamčení extranetu](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Sestava neúspěšných přihlášení](how-to-connect-health-adfs-risky-ip.md)</br>[V souladu s ochranou osobních údajů](reference-connect-health-user-privacy.md)|
|Získání upozornění na [všechny kritické problémy systému služby ADFS](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Konfigurace serveru a dostupnost</br>[Výkon a možnosti připojení](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Pravidelná údržba|
|Snadné nasazení a Správa|[Rychlá instalace agenta](how-to-connect-health-agent-install.md#install-the-agent-for-ad-fs)</br>Automatický upgrade agenta na nejnovější verzi</br>Dostupnost dat na portálu během několika minut|
Detailní [metriky využití](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|Využití nejčastějších aplikací</br>Umístění v síti a připojení TCP</br>Žádosti o tokeny pro každý server|
|Skvělé uživatelské prostředí|Styl řídicího panelu z webu Azure Portal</br>[Upozornění prostřednictvím e-mailů](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>Licenční požadavky pro použití Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Licenční požadavky pro použití Azure AD Connect Health
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další kroky

- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)
- [Instalace agentů Azure AD Connect Health](how-to-connect-health-agent-install.md)
