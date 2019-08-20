---
title: Kurz – vytvoření instance Azure Active Directory Domain Services | Microsoft Docs
description: V tomto kurzu se naučíte, jak vytvořit a nakonfigurovat instanci Azure Active Directory Domain Services pomocí Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: cc062f830facb0d617dc649ecd17acfff0a740af
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619191"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Kurz: Vytvoření a konfigurace instance Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP, ověřování Kerberos/NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory. Tyto doménové služby spotřebujete bez nutnosti nasazovat, spravovat a opravovat řadiče domény sami. Služba Azure služba AD DS se integruje s vaším stávajícím tenant Azure AD. Tato integrace umožňuje uživatelům přihlásit se pomocí svých podnikových přihlašovacích údajů a pomocí existujících skupin a uživatelských účtů můžete zabezpečit přístup k prostředkům.

V tomto kurzu se dozvíte, jak vytvořit a nakonfigurovat instanci Azure služba AD DS pomocí Azure Portal.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace nastavení DNS a virtuální sítě pro spravovanou doménu
> * Vytvoření instance Azure AD DS
> * Přidání uživatelů s právy pro správu do správy domén
> * Povolit synchronizaci hodnot hash hesel

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Abyste mohli Azure služba AD DS povolit, potřebujete ve svém tenantovi Azure AD oprávnění *globálního správce* .
* Abyste mohli vytvořit požadované prostředky Azure služba AD DS, potřebujete oprávnění přispěvatele v předplatném Azure.
* [Pro Samoobslužné resetování hesla][configure-sspr]se musí nakonfigurovat tenant služby Azure AD.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a nakonfigurujete instanci Azure služba AD DS pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Vytvoření instance a Konfigurace základního nastavení

Chcete-li spustit průvodce **povolením Azure AD Domain Services** , proveďte následující kroky:

1. V levém horním rohu Azure Portal vyberte **+ vytvořit prostředek**.
1. Do panelu hledání zadejte *Domain Services* a pak zvolte *Azure AD Domain Services* z návrhů hledání.
1. Na stránce Azure AD Domain Services vyberte **vytvořit**. Spustí se průvodce **povolením Azure AD Domain Services** .

Když vytváříte instanci Azure služba AD DS, zadáte název DNS. Při volbě tohoto názvu DNS máte nějaké okolnosti:

* **Název předdefinované domény:** Ve výchozím nastavení se používá integrovaný název domény adresáře (přípona *. onmicrosoft.com* ). Pokud chcete povolit přístup přes Internet k spravované doméně pomocí protokolu Secure LDAP, nemůžete vytvořit digitální certifikát pro zabezpečení připojení s touto výchozí doménou. Společnost Microsoft vlastní doménu *. onmicrosoft.com* , takže certifikační autorita (CA) certifikát nevydá.
* **Vlastní názvy domén:** Nejběžnějším přístupem je zadat vlastní název domény, obvykle ten, který už vlastníte a který je směrovatelný. Když použijete směrovatelný, vlastní doménu, může provoz správně přesměrovat podle potřeby na podporu vašich aplikací.
* **Přípony domén bez směrování:** Obecně doporučujeme, abyste se vyhnuli příponě názvu domény, která není směrovatelný, například *contoso. Local*. Přípona *. Local* není směrovatelný a může způsobit problémy s překladem názvů DNS.

Platí taky následující omezení názvů DNS:

* **Omezení prefixu domény:** Nelze vytvořit spravovanou doménu s předponou delší než 15 znaků. Předpona zadaného názvu domény (například *Contoso* v názvu domény *contoso.com* ) musí obsahovat maximálně 15 znaků.
* **Konflikty síťových názvů:** Název domény DNS pro spravovanou doménu už ve virtuální síti neexistuje. Konkrétně se podívejte na následující scénáře, které by mohly vést ke konfliktu názvů:
    * Pokud již máte doménu služby Active Directory se stejným názvem domény DNS ve službě Azure Virtual Network.
    * Pokud má virtuální síť, ve které plánujete povolit spravovanou doménu, připojení VPN s vaší místní sítí. V tomto scénáři se ujistěte, že nemáte doménu se stejným názvem domény DNS ve vaší místní síti.
    * Pokud máte existující cloudovou službu Azure s tímto názvem ve službě Azure Virtual Network.

Dokončete pole v okně *základy* Azure Portal a vytvořte instanci Azure služba AD DS:

1. Zadejte **název domény DNS** pro spravovanou doménu a vezměte v úvahu předchozí body.
1. Vyberte **předplatné** Azure, ve kterém chcete vytvořit spravovanou doménu.
1. Vyberte **skupinu prostředků** , do které má spravovaná doména patřit. Zvolte možnost **vytvořit novou** nebo vybrat existující skupinu prostředků.
1. Vyberte **umístění** Azure, ve kterém se má spravovaná doména vytvořit.
1. Kliknutím na tlačítko **OK** přejděte k části **síť** .

![Konfigurace základního nastavení instance Azure AD Domain Services](./media/tutorial-create-instance/basics-window.png)

## <a name="create-and-configure-the-virtual-network"></a>Vytvoření a konfigurace virtuální sítě

Pro zajištění připojení je potřeba virtuální síť Azure a vyhrazená podsíť. V této podsíti virtuální sítě je povolený Azure služba AD DS. V tomto kurzu vytvoříte virtuální síť, ale můžete se rozhodnout použít stávající virtuální síť. V obou případech musíte vytvořit vyhrazenou podsíť pro použití v Azure služba AD DS.

Některé z informací pro tuto vyhrazenou podsíť virtuální sítě zahrnují následující oblasti:

* Aby bylo možné podporovat prostředky Azure služba AD DS, musí mít podsíť aspoň 3-5 dostupné IP adresy v rozsahu adres.
* Nevybírejte podsíť *brány* pro nasazení služby Azure služba AD DS. Nasazení Azure služba AD DS do podsítě *brány* se nepodporuje.
* Nesaďte do podsítě žádné další virtuální počítače. Aplikace a virtuální počítače často používají skupiny zabezpečení sítě k zabezpečení připojení. Spuštění těchto úloh v samostatné podsíti vám umožní použít tyto skupiny zabezpečení sítě bez přerušení připojení ke svojí spravované doméně.
* Po povolení služby Azure služba AD DS nemůžete svou spravovanou doménu přesunout do jiné virtuální sítě.

Další informace o tom, jak naplánovat a nakonfigurovat virtuální síť, najdete v tématu [požadavky na síť pro Azure Active Directory Domain Services][network-considerations].

Vyplňte pole v okně *síť* následujícím způsobem:

1. V okně **síť** zvolte **možnost vybrat virtuální síť**.
1. V tomto kurzu se rozhodnete **vytvořit novou** virtuální síť pro nasazení Azure služba AD DS do.
1. Zadejte název virtuální sítě, třeba *myVnet*, a pak zadejte rozsah adres, například *10.1.0.0/16*.
1. Vytvořte vyhrazenou podsíť s jasným názvem, například *DomainServices*. Zadejte rozsah adres, například *10.1.0.0/24*.

    ![Vytvoření virtuální sítě a podsítě pro použití s Azure AD Domain Services](./media/tutorial-create-instance/create-vnet.png)

    Nezapomeňte vybrat rozsah adres, který se nachází v rámci vašeho privátního rozsahu IP adres. Rozsahy IP adres, které nevlastníte, jsou ve veřejném adresním prostoru, což způsobí chyby v Azure služba AD DS.

    > [!TIP]
    > Na stránce **Vybrat virtuální síť** se zobrazují existující virtuální sítě, které patří do skupiny prostředků a do umístění Azure, které jste dříve vybrali. Před nasazením Azure služba AD DS je nutné [vytvořit vyhrazenou podsíť][create-dedicated-subnet] .

1. Po vytvoření virtuální sítě a podsítě by se měla automaticky vybrat podsíť, například *DomainServices*. Místo toho můžete zvolit alternativní existující podsíť, která je součástí vybrané virtuální sítě:

    ![Volba vyhrazené podsítě v rámci virtuální sítě](./media/tutorial-create-instance/choose-subnet.png)

1. Výběrem **OK** potvrďte konfiguraci virtuální sítě.

## <a name="configure-an-administrative-group"></a>Konfigurace skupiny pro správu

Pro správu domény Azure služba AD DS se používá speciální skupina pro správu s názvem *AAD DC Administrators* . Členům této skupiny se udělují oprávnění správce na virtuálních počítačích, které jsou připojené k doméně spravované domény. V případě virtuálních počítačů připojených k doméně se tato skupina přidá do místní skupiny Administrators. Členové této skupiny se taky můžou pomocí vzdálené plochy vzdáleně připojit k virtuálním počítačům připojeným k doméně.

Nemáte oprávnění *správce domény* nebo *správce podniku* ve spravované doméně pomocí Azure služba AD DS. Tato oprávnění jsou vyhrazena službou a nejsou zpřístupněna uživatelům v rámci tenanta. Místo toho vám skupina *správců řadiče domény AAD* umožňuje provádět některé privilegované operace. Mezi tyto operace patří připojení počítačů k doméně, patřící do skupiny pro správu na virtuálních počítačích připojených k doméně a konfigurace Zásady skupiny.

Průvodce automaticky vytvoří skupinu *AAD DC Administrators* v adresáři Azure AD. Pokud máte ve svém adresáři služby Azure AD existující skupinu s tímto názvem, průvodce tuto skupinu vybere. Volitelně můžete zvolit přidání dalších uživatelů do této skupiny *Správci AAD DC* během procesu nasazení. Tyto kroky můžete provést později.

1. Pokud chcete přidat další uživatele do této skupiny *AAD DC Administrators* , vyberte **spravovat členství ve skupině**.
1. Vyberte tlačítko **přidat členy** a pak vyhledejte a vyberte uživatele z adresáře Azure AD. Vyhledávejte například svůj vlastní účet a přidejte ho do skupiny *správci řadiče domény AAD* .

    ![Konfigurace členství ve skupině pro skupinu správci řadiče domény AAD](./media/tutorial-create-instance/admin-group.png)

1. Až to bude hotové, vyberte **OK**.

## <a name="configure-synchronization"></a>Konfigurace synchronizace

Azure služba AD DS umožňuje synchronizovat *všechny* uživatele a skupiny, které jsou dostupné ve službě Azure AD, nebo jenom *vymezenou* synchronizaci jenom konkrétních skupin. Pokud se rozhodnete synchronizovat *všechny* uživatele a skupiny, nemůžete se později rozhodnout jenom provést synchronizaci s vymezeným oborem. Další informace o vymezené synchronizaci najdete v tématu [Azure AD Domain Services s vymezeným rozsahem synchronizace][scoped-sync].

1. V tomto kurzu se rozhodnete synchronizovat **všechny** uživatele a skupiny. Tato volba synchronizace je výchozí možností.

    ![Provedení úplné synchronizace uživatelů a skupin ze služby Azure AD](./media/tutorial-create-instance/sync-all.png)

1. Vyberte **OK**.

## <a name="deploy-your-managed-domain"></a>Nasazení spravované domény

Na stránce **Souhrn** v průvodci zkontrolujte nastavení konfigurace pro spravovanou doménu. Chcete-li provést změny, můžete přejít zpět na libovolný krok průvodce.

1. Pokud chcete vytvořit spravovanou doménu, vyberte **OK**.
1. Proces zřizování spravované domény může trvat až hodinu. Na portálu se zobrazí oznámení, ve kterém se zobrazuje průběh nasazení služby Azure služba AD DS. Vyberte oznámení, abyste viděli podrobný průběh nasazení.

    ![Oznámení v Azure Portal probíhajícího nasazení](./media/tutorial-create-instance/deployment-in-progress.png)

1. Vyberte skupinu prostředků, třeba *myResourceGroup*, a pak vyberte instanci Azure služba AD DS ze seznamu prostředků Azure, jako je třeba *contoso.com*. Na kartě **Přehled** se zobrazuje, že se spravovaná doménaaktuálně nasazuje. Nemůžete nakonfigurovat spravovanou doménu, dokud není plně zřízené.

    ![Stav služby Domain Services ve stavu zřizování](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Když je spravovaná doména plně zřízena, karta **Přehled** zobrazuje stav domény jako spuštěno.

    ![Stav služby Domain Services po úspěšném zřízení](./media/tutorial-create-instance/successfully-provisioned.png)

Během procesu zřizování vytvoří Azure služba AD DS v adresáři dvě podnikové aplikace s názvem *služby řadiče domény* a *AzureActiveDirectoryDomainControllerServices* . Tyto podnikové aplikace jsou potřeba k obsluhování vaší spravované domény. Je nezbytné, aby tyto aplikace nebyly odstraněny kdykoli.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Aktualizace nastavení DNS pro virtuální síť Azure

Po úspěšném nasazení Azure služba AD DS nyní nakonfigurujte virtuální síť tak, aby povolovala jiným připojeným virtuálním počítačům a aplikacím používat spravovanou doménu. Pokud chcete toto připojení poskytnout, aktualizujte nastavení serveru DNS virtuální sítě tak, aby odkazovalo na dvě IP adresy, ve kterých je nasazená služba Azure služba AD DS.

1. Karta **Přehled** pro spravovanou doménu zobrazuje některé **požadované kroky konfigurace**. První krok konfigurace je aktualizovat nastavení serveru DNS pro vaši virtuální síť. Po správné konfiguraci nastavení DNS se tento krok už nezobrazuje.

    Uvedené adresy jsou řadiče domény pro použití ve virtuální síti. V tomto příkladu jsou tyto adresy *10.1.0.4* a *10.1.0.5*. Tyto IP adresy můžete později najít na kartě **vlastnosti** .

    ![Konfigurace nastavení DNS pro vaši virtuální síť s Azure AD Domain Services IP adresami](./media/tutorial-create-instance/configure-dns.png)

1. Pokud chcete aktualizovat nastavení serveru DNS pro virtuální síť, klikněte na tlačítko **Konfigurovat** . Nastavení DNS se automaticky nakonfigurují pro vaši virtuální síť.

> [!TIP]
> Pokud jste v předchozích krocích vybrali existující virtuální síť, všechny virtuální počítače připojené k síti získají po restartování jenom nové nastavení DNS. Virtuální počítače můžete restartovat pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Povolení uživatelských účtů pro Azure služba AD DS

K ověřování uživatelů ve spravované doméně služba AD DS Azure potřebuje hodnoty hash hesel ve formátu, který je vhodný pro ověřování pomocí protokolu NTLM (NT LAN Manager) a Kerberos. Azure AD negeneruje nebo ukládá hodnoty hash hesel ve formátu, který je vyžadován pro ověřování protokolem NTLM nebo Kerberos, dokud nepovolíte služba AD DS Azure pro vašeho tenanta. Z bezpečnostních důvodů Azure AD také neukládá přihlašovací údaje hesla ve formě nešifrovaných textů. Proto služba Azure AD nemůže automaticky generovat tyto hodnoty hash hesla NTLM nebo Kerberos na základě stávajících přihlašovacích údajů uživatelů.

> [!NOTE]
> Po správné konfiguraci se použitelné hodnoty hash hesel ukládají do spravované domény Azure služba AD DS. Pokud odstraníte spravovanou doménu Azure služba AD DS, odstraní se i všechny hodnoty hash hesel uložené v tomto okamžiku. Informace o synchronizovaných přihlašovacích údajích ve službě Azure AD se nedají znovu použít, pokud později vytvoříte Azure služba AD DS spravované domény – musíte znovu nakonfigurovat synchronizaci hodnot hash hesel, aby se znovu ukládaly hodnoty hash hesel. Virtuální počítače připojené k doméně nebo uživatelé nebudou moct hned ověřit – Azure AD potřebuje vygenerovat a uložit hodnoty hash hesel v nové spravované doméně Azure služba AD DS. Další informace najdete v tématu [proces synchronizace hodnot hash hesel pro Azure služba AD DS a Azure AD Connect][password-hash-sync-process].

Postup generování a ukládání hodnot hash hesel se liší pro uživatelské účty, které jsou vytvořené v Azure AD, oproti uživatelským účtům, které jsou synchronizované z místního adresáře pomocí Azure AD Connect. Uživatelský účet jenom cloudu je účet vytvořený v adresáři služby Azure AD pomocí webu Azure Portal nebo rutin Azure AD PowerShellu. Tyto uživatelské účty se nesynchronizují z místního adresáře. V tomto kurzu budeme pracovat se základním uživatelským účtem jenom pro Cloud. Další informace o dalších krocích potřebných pro použití Azure AD Connect najdete v tématu [synchronizace hodnot hash hesel u uživatelských účtů synchronizovaných z místní služby AD do spravované domény][on-prem-sync].

> [!TIP]
> Pokud má tenant služby Azure AD kombinaci uživatelů jenom pro Cloud a uživatelů z místní služby AD, musíte dokončit obě sady kroků.

U uživatelských účtů jenom pro Cloud musí uživatelé změnit svoje heslo, aby mohli používat Azure služba AD DS. Tento proces změny hesla způsobí, že se ve službě Azure AD vygenerují a ukládají hodnoty hash hesel pro ověřování pomocí protokolu Kerberos a NTLM. Můžete buď ukončit platnost hesel pro všechny uživatele v tenantovi, kteří potřebují používat Azure služba AD DS, což vynutí změnu hesla při příštím přihlášení, nebo jim dát pokyn k ruční změně hesla. V tomto kurzu ručně změníte heslo uživatele.

Než bude moct uživatel resetovat heslo, musí být tenant služby Azure AD [nakonfigurovaný pro Samoobslužné resetování hesla][configure-sspr].

Chcete-li změnit heslo pouze pro cloudového uživatele, musí uživatel provést následující kroky:

1. Přejděte na stránku přístupového panelu Azure AD na [https://myapps.microsoft.com](https://myapps.microsoft.com)adrese.
1. V pravém horním rohu vyberte své jméno a pak v rozevírací nabídce vyberte možnost **profil** .

    ![Výběr profilu](./media/tutorial-create-instance/select-profile.png)

1. Na stránce **profil** vyberte **změnit heslo**.
1. Na stránce **změnit heslo** zadejte stávající (staré) heslo a pak zadejte a potvrďte nové heslo.
1. Vyberte **odeslat**.

Může to trvat několik minut, než se změní heslo pro nové heslo, aby bylo možné použít v Azure služba AD DS. Po přibližně 20 minutách můžete nové heslo použít k přihlášení do počítačů připojených ke spravované doméně.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace nastavení DNS a virtuální sítě pro spravovanou doménu
> * Vytvoření instance Azure AD DS
> * Přidání uživatelů s právy pro správu do správy domén
> * Povolení uživatelských účtů pro Azure služba AD DS a generování hodnot hash hesel

Pokud chcete tuto spravovanou doménu zobrazit v akci, vytvořte virtuální počítač a připojte se k doméně.

> [!div class="nextstepaction"]
> [Připojení virtuálního počítače s Windows serverem k spravované doméně](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
