---
title: Nejčastější dotazy k Windows 10 Enterprise s více relacemi – Azure
description: Nejčastější dotazy a doporučené postupy pro používání vícerelací Windows 10 Enterprise pro Virtuální plochu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d7ea99e2ee8e2882c211ee17acec70222dc058a8
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637097"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Nejčastější dotazy k Windows 10 Enterprise pro více relací

Tento článek odpovídá na nejčastější dotazy a vysvětluje osvědčené postupy pro vícerelací Windows 10 Enterprise.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Co je vícerelací Windows 10 Enterprise?

Windows 10 Enterprise multi-session, dříve známý jako Windows 10 Enterprise pro virtuální plochy (EVD), je nový hostitel relací vzdálené plochy, který umožňuje více souběžných interaktivních relací. Dříve to mohl udělat pouze systém Windows Server. Tato funkce poskytuje uživatelům známé prostředí windows 10, zatímco IT může těžit z výhod nákladů více relací a používat stávající licence systému Windows pro jednotlivé uživatele namísto licencí klientského přístupu rds (CALs). Další informace o licencích a cenách naleznete v [tématu Ceny virtuální plochy windows](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Kolik uživatelů může mít současně interaktivní relaci ve vícerelacích Windows 10 Enterprise?

Kolik interaktivních relací, které mohou být aktivní současně, závisí na hardwarových prostředcích vašeho systému (vCPU, paměť, disk a vGPU), jak uživatelé používají své aplikace při přihlášení k relaci a jak je zatížení systému náročné. Doporučujeme ověřit výkon systému, abyste pochopili, kolik uživatelů můžete mít ve windows 10 Enterprise multi-session. Další informace najdete v tématu [Ceny virtuální plochy windows](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Proč moje aplikace hlásí windows 10 Enterprise multi-session jako operační systém Server?

Windows 10 Enterprise multi-session je virtuální edice Windows 10 Enterprise. Jedním z rozdílů je, že tento operační systém (OS) hlásí [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) jako s hodnotou 3, stejnou hodnotu jako Windows Server. Tato vlastnost udržuje operační systém kompatibilní s existujícími nástroji pro správu RDSH, aplikacemi rdsh podporujícími více relací a většinou optimalizací výkonu systému nízké úrovně pro prostředí RDSH. Některé instalační programy aplikací mohou blokovat instalaci v systému Windows 10 s více relacemi v závislosti na tom, zda zjistí, že producttype je nastaven na klienta. Pokud se vaše aplikace nenainstaluje, požádejte dodavatele aplikace o aktualizovanou verzi. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Můžu windows 10 Enterprise spustit více relací v místním prostředí?

Windows 10 Enterprise multi-session nelze spustit v místním produkčním prostředí, protože je optimalizované pro službu Windows Virtual Desktop pro Azure. Je proti licenční smlouvě spouštět Windows 10 Enterprise více relací mimo Azure pro produkční účely. Windows 10 Enterprise multi-session se neaktivuje proti místním službám správy klíčů (KMS).
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Jak můžu přizpůsobit image windows 10 Enterprise pro více relací pro svou organizaci?

Virtuální počítač (VM) můžete spustit v Azure s Windows 10 Windows 10 Enterprise multi-session a přizpůsobit ho instalací obchodních aplikací, sysprepem/generalize a pak vytvořit bitovou kopii pomocí portálu Azure.  
 
Pokud chcete začít, vytvořte virtuální počítač v Azure s Windows 10 Enterprise s vícerelací. Místo spuštění virtuálního počítače v Azure, můžete stáhnout virtuální pevný disk přímo. Poté budete moct stažený virtuální disk Použít k vytvoření nového virtuálního počítače generace 1 na počítači s Windows 10 s povoleným technologiem Hyper-V.

Přizpůsobte bitovou kopii potřebám instalací obchodních aplikací a programu sysprep. Po dokončení přizpůsobení nahrajte image do Azure s virtuálním pevným diskem uvnitř. Poté získejte virtuální plochu Windows z Azure Marketplace a použijte ji k nasazení nového fondu hostitelů s přizpůsobenou bitovou kopií.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Jak můžu po nasazení spravovat vícerelací Windows 10 Enterprise?

Můžete použít libovolný podporovaný konfigurační nástroj, ale doporučujeme nástroj Configuration Manager verze 1906, protože podporuje vícerelaci systému Windows 10 Enterprise. V současné době pracujeme na podpoře Microsoft Intune.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Může být Windows 10 Enterprise s více relací připojen k Azure Active Directory (AD)?

Windows 10 Enterprise multi-session je momentálně podporovaná jako hybridní Azure AD připojená. Po windows 10 Enterprise vícerelací je připojen k doméně, použijte existující objekt zásad skupiny k povolení registrace Azure AD. Další informace najdete v [tématu Plánování hybridní implementace připojení k službě Azure Active Directory](../active-directory/devices/hybrid-azuread-join-plan.md).
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Kde najdu image windows 10 Enterprise s více relacemi?

Windows 10 Enterprise multi-session je v galerii Azure. Pokud ho chcete najít, přejděte na portál Azure a vyhledejte verzi Windows 10 Enterprise for Virtual Desktops. Obrázek integrovaný s Office Pro Plus najdete na webu Azure Portal a vyhledejte Microsoft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Kterou bitovou kopii windows 10 Enterprise pro více relací mám použít?

Galerie Azure má několik verzí, včetně Windows 10 Enterprise multi-session, verze 1809 a Windows 10 Enterprise multi-session, verze 1903. Pro zlepšení výkonu a spolehlivosti doporučujeme používat nejnovější verzi.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Které verze Windows 10 Enterprise pro více relací jsou podporované?

Windows 10 Enterprise multi-session, verze 1809 a novější jsou podporované a jsou dostupné v galerii Azure. Tyto verze se řídí stejnými zásadami životního cyklu podpory jako Windows 10 Enterprise, což znamená, že jarní verze je podporována po dobu 18 měsíců a podzimní verze po dobu 30 měsíců.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Jaké řešení pro správu profilů mám použít pro vícerelaci Windows 10 Enterprise?

Doporučujeme používat kontejnery profilu FSLogix při konfiguraci Systému Windows 10 Enterprise v netrvalých prostředích nebo jiných scénářích, které potřebují centrálně uložený profil. FSLogix zajišťuje, že profil uživatele je k dispozici a aktuální pro každou relaci uživatele. Doporučujeme také použít kontejner profilu FSLogix k uložení profilu uživatele v libovolné sdílené složce SMB s příslušnými oprávněními, ale v případě potřeby můžete uložit profily uživatelů do úložiště objektů blob stránky Azure. Uživatelé virtuální chodnících systému Windows mohou používat zařízení FSLogix bez dalších nákladů.
 
Další informace o konfiguraci kontejneru profilu FSLogix naleznete [v tématu Konfigurace kontejneru profilu FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Kterou licenci potřebuji pro přístup k vícerelaci Windows 10 Enterprise?

Úplný seznam příslušných licencí naleznete v [tématu Ceny virtuální plochy systému Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Proč moje aplikace zmizí po odhlášení?

K tomu dochází, protože používáte windows 10 Enterprise multi-session s řešením pro správu profilů, jako je FSLogix. Vaše řešení pro správce nebo profil nakonfigurovalo systém tak, aby při odhlášení uživatelů odstraňoval profily uživatelů. Tato konfigurace znamená, že když systém odstraní váš uživatelský profil po odhlášení, odstraní také všechny aplikace, které jste nainstalovali během relace. Pokud chcete zachovat nainstalované aplikace, budete muset požádat správce, aby tyto aplikace zřazoval pro všechny uživatele v prostředí Virtuální plochy Windows.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Jak zajistím, aby aplikace nezmizely, když se uživatelé odhlásí?

Většina virtualizovaných prostředí je ve výchozím nastavení nakonfigurována tak, aby uživatelům zabránila v instalaci dalších aplikací do jejich profilů. Pokud se chcete ujistit, že aplikace nezmizí, když se uživatel odhlásí z Windows Virtual Desktop, musíte tuto aplikaci zřídit pro všechny uživatelské profily ve vašem prostředí. Další informace o zřizování aplikací najdete v těchto zdrojích:

- [Publikování integrovaných aplikací ve Windows Virtual Desktop](publish-apps.md)
- [Možnosti příkazového řádku pro obsluhu balíčků aplikací DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Balíček AddxProvisionedPackage](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Jak zajistím, aby uživatelé nestahovali a neinstalovali aplikace z Microsoft Storu?

Aplikaci Microsoft Store můžete zakázat, abyste měli jistotu, že uživatelé nebudou stahovat další aplikace nad rámec aplikací, které jste pro ně už zříditi.

Zakázání aplikace Store:

1. Vytvořte nové zásady skupiny.
2. Vyberte možnost Šablony**pro správu** >  **konfigurace** > počítače**Součásti systému Windows**.
3. Vyberte **Uložit**.
4. Vyberte **Uložit aplikaci**.
5. Vyberte **Zakázáno**, pak vyberte **OK**.
6. Vyberte **Použít**.
 
## <a name="next-steps"></a>Další kroky

Další informace o vícerelací Windows Virtual Desktop a Windows 10 Enterprise:

- Přečtěte si naši [dokumentaci k Windows Virtual Desktop Preview](overview.md)
- Navštivte naši [technickou komunitu virtuálních desktopů pro Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Nastavení nasazení virtuální plochy systému Windows pomocí [výukových programů virtuální plochy systému Windows](tenant-setup-azure-active-directory.md)
