---
title: Nejčastější dotazy k Windows 10 Enterprise pro více relací – Azure
description: Nejčastější dotazy a osvědčené postupy pro používání více relací Windows 10 Enterprise pro virtuální počítače s Windows
author: Heidilohr
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aafac1133b2bee54624d5f0b00061c5332fcd90b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631876"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Nejčastější dotazy k Windows 10 Enterprise pro více relací

Tento článek obsahuje odpovědi na nejčastější dotazy a vysvětluje osvědčené postupy pro více relací Windows 10 Enterprise.

## <a name="what-is-windows-10-enterprise-multi-session"></a>Co je více relací Windows 10 Enterprise?

Windows 10 Enterprise s více relacemi, dříve označované jako Windows 10 Enterprise pro virtuální plochy (EVD), je nový Hostitel relace vzdálené plochy, který umožňuje více souběžných interaktivních relací. Dřív to mohl udělat jenom Windows Server. Tato funkce poskytuje uživatelům známé prostředí Windows 10, které může využívat výhod nákladů na více relací a používat stávající licencování Windows na uživatele namísto licencí pro klientský přístup k VP (CAL). Další informace o licencích a cenách najdete v tématu [ceny pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Kolik uživatelů může současně mít interaktivní relaci v rámci více relací Windows 10 Enterprise?

Kolik interaktivních relací, které může být aktivní současně, spoléhá na hardwarové prostředky vašeho systému (vCPU, paměť, disk a vGPU), jak vaši uživatelé používají své aplikace při přihlášení k relaci a na tom, jak vysoké je zatížení vašeho systému. Doporučujeme, abyste ověřili výkon vašeho systému, abyste porozuměli tomu, kolik uživatelů můžete mít v rámci více relací Windows 10 Enterprise. Další informace najdete v tématu [ceny pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Proč moje aplikace sestaví více relací Windows 10 Enterprise jako serverový operační systém?

Windows 10 Enterprise Multi-Session je virtuální edice Windows 10 Enterprise. Jednou z rozdílů je, že tento operační systém (OS) hlásí [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) jako hodnotu 3, což je stejná hodnota jako Windows Server. Tato vlastnost udržuje kompatibilní s operačním systémem s existujícími nástroji pro správu vzdálené plochy, aplikacemi nepracujících s více relacemi a převážně optimalizacemi výkonu systému nižší úrovně pro prostředí vzdálené plochy. Některé instalační programy aplikací můžou blokovat instalaci ve více relacích s Windows 10 v závislosti na tom, jestli ProductType je nastavená na Client. Pokud se vaše aplikace nenainstaluje, obraťte se na dodavatele aplikace a požádejte o aktualizovanou verzi.

## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Můžu používat Windows 10 Enterprise s více relacemi v místním prostředí?

Windows 10 Enterprise multi-session nemůže běžet v místních produkčních prostředích, protože je optimalizovaná pro službu Virtual Desktop systému Windows pro Azure. Je v souladu s licenční smlouvou pro provozování více relací Windows 10 Enterprise mimo Azure pro produkční účely. Windows 10 Enterprise multi-session se neaktivuje proti místním službám správy klíčů (KMS).

## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Návody přizpůsobit image pro více relací Windows 10 Enterprise pro moji organizaci?

Můžete spustit virtuální počítač v Azure s Windows 10 Enterprise multi-session a přizpůsobit ho tak, že nainstalujete obchodní aplikace, Sysprep/generalize a pak vytvoříte image pomocí Azure Portal.

Pokud chcete začít, vytvořte v Azure virtuální počítač s více relacemi Windows 10 Enterprise. Místo spuštění virtuálního počítače v Azure si můžete virtuální pevný disk stáhnout přímo. Potom budete moct použít stažený disk VHD k vytvoření nového virtuálního počítače 1. generace v počítači s Windows 10 s povolenou technologií Hyper-V.

Přizpůsobte si bitovou kopii podle svých potřeb instalací obchodních aplikací a pomocí nástroje Sysprep image. Až budete hotovi s přizpůsobením, nahrajte image do Azure s virtuálním pevným diskem v. Pak z Azure Marketplace získat virtuální klient Windows a použít ho k nasazení nového fondu hostitelů s přizpůsobenou imagí.

## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Návody spravovat více relací Windows 10 Enterprise po nasazení?

Můžete použít libovolný podporovaný konfigurační nástroj, ale doporučujeme Configuration Manager verzi 1906, protože podporuje víc relací Windows 10 Enterprise. V současnosti pracujeme na podpoře Microsoft Intune.

## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Může se Azure Active Directory (AD) Windows 10 Enterprise víc relací připojit?

Pro Windows 10 Enterprise s více relacemi se aktuálně podporuje hybridní Azure AD – připojeno. Po připojení Windows 10 Enterprise k doméně použijte existující objekt Zásady skupiny k povolení registrace Azure AD. Další informace najdete v tématu [Plánování implementace služby hybrid Azure Active Directory JOIN](../active-directory/devices/hybrid-azuread-join-plan.md).

## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Kde najdu image pro Windows 10 Enterprise s více relacemi?

Víc relací Windows 10 Enterprise je v galerii Azure. Pokud ho chcete najít, přejděte na Azure Portal a vyhledejte verzi Windows 10 Enterprise pro virtuální počítače. Pro Image integrovanou s Microsoft 365 aplikacemi pro podniky použijte Azure Portal a vyhledejte aplikace **Microsoft Windows 10 + Microsoft 365 pro podniky**.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Kterou image Windows 10 Enterprise pro více relací mám použít?

Galerie Azure obsahuje několik verzí, včetně Windows 10 Enterprise multi-session, verze 1809 a Windows 10 Enterprise multi-session verze 1903. Pro zvýšení výkonu a spolehlivosti doporučujeme použít nejnovější verzi.

## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Které verze více relací Windows 10 Enterprise jsou podporované?

Windows 10 Enterprise multi-session, verze 1809 a novější se podporují a jsou dostupné v galerii Azure. Tyto verze se řídí stejnými zásadami životního cyklu podpory jako Windows 10 Enterprise, což znamená, že verze v březnu je podporovaná na 18 měsíců a v září vydání po dobu 30 měsíců.

## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Které řešení pro správu profilů mám použít pro více relací Windows 10 Enterprise?

Pokud konfigurujete Windows 10 Enterprise v netrvalých prostředích nebo v jiných scénářích, které potřebují centrálně uložený profil, doporučujeme používat kontejnery profilů FSLogix. FSLogix zajišťuje, aby byl profil uživatele k dispozici a byl aktuální pro každou relaci uživatele. Doporučujeme vám také použít váš kontejner profilu FSLogix k uložení profilu uživatele v jakékoli sdílené složce protokolu SMB s příslušnými oprávněními, ale v případě potřeby můžete profily uživatelů ukládat v úložišti objektů blob stránky Azure. Uživatelé virtuálních klientů Windows můžou FSLogix používat bez dalších poplatků.  FSLogix je součástí všech imagí Windows 10 Enterprise s více relacemi, ale správce IT pořád zodpovídá za konfiguraci kontejneru profilu FSLogix.

Další informace o tom, jak nakonfigurovat kontejner profilu FSLogix, najdete v tématu [Konfigurace kontejneru profilu FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Kterou licenci potřebuji pro přístup k více relacím Windows 10 Enterprise?

Úplný seznam použitelných licencí najdete v tématu [ceny pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Proč zmizely moje aplikace po odhlášení?

K tomu dochází, protože používáte víc relací Windows 10 Enterprise s řešením správy profilů, jako je FSLogix. Vaše řešení pro správu nebo profil nakonfigurovalo systém tak, aby při odhlášení uživatele odstranil profily uživatelů. Tato konfigurace znamená, že když po odhlášení systém odstraní svůj uživatelský profil, odebere také všechny aplikace, které jste během vaší relace nainstalovali. Pokud chcete zachovat aplikace, které jste nainstalovali, musíte požádat správce, aby tyto aplikace zřídil pro všechny uživatele ve vašem prostředí virtuálních počítačů s Windows.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Návody zajistěte, aby aplikace nezmizely při odhlášení uživatele?

Většina virtualizovaných prostředí se ve výchozím nastavení konfiguruje, aby uživatelé nemohli instalovat další aplikace do jejich profilů. Pokud se chcete ujistit, že aplikace nezmizí, když se uživatel odhlásí z virtuálního klienta Windows, musíte tuto aplikaci zřídit pro všechny uživatelské profily ve vašem prostředí. Další informace o zřizování aplikací najdete v těchto materiálech:

- [Publikování integrovaných aplikací ve virtuálním počítači s Windows](publish-apps.md)
- [Možnosti příkazového řádku obsluhy balíčku aplikace DISM](/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](/powershell/module/dism/add-appxprovisionedpackage)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Návody zajistěte, aby si uživatelé nestáhli a neinstalovali aplikace z Microsoft Store?

Aplikaci Microsoft Store můžete zakázat, abyste se ujistili, že uživatelé nestahují extra aplikace nad rámec aplikací, které pro ně jste už zřídili.

Zakázání aplikace pro Store:

1. Vytvoří nový Zásady skupiny.
2. Vyberte **Konfigurace počítače**  >  **šablony pro správu**  >  **součásti systému Windows**.
3. Vyberte **Store**.
4. Vyberte **aplikace pro Store**.
5. Vyberte **disabled (zakázáno**) a pak vyberte **OK**.
6. Vyberte **Použít**.

## <a name="next-steps"></a>Další kroky

Další informace o virtuálních počítačích s Windows a Windows 10 Enterprise s více relacemi:

- Přečtěte si [dokumentaci k virtuálním plochám Windows](overview.md)
- Navštívit naši [TechCommunity virtuálního počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Kurzy k instalaci virtuálních klientských počítačů s Windows pomocí [kurzů pro virtuální počítače s Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)
