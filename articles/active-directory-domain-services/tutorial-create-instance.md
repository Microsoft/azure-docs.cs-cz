---
title: Kurz – vytvoření instance Azure Active Directory Domain Services | Microsoft Docs
description: V tomto kurzu se naučíte, jak vytvořit a nakonfigurovat instanci Azure Active Directory Domain Services pomocí Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 2a1fbe8d47af8a2215b0d0a3d81fbe67a62d4755
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474401"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Kurz: vytvoření a konfigurace instance Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP, ověřování Kerberos/NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory. Tyto doménové služby spotřebujete bez nutnosti nasazovat, spravovat a opravovat řadiče domény sami. Služba Azure služba AD DS se integruje s vaším stávajícím tenant Azure AD. Tato integrace umožňuje uživatelům přihlásit se pomocí svých podnikových přihlašovacích údajů a pomocí existujících skupin a uživatelských účtů můžete zabezpečit přístup k prostředkům.

Pomocí výchozích možností konfigurace pro sítě a synchronizaci můžete vytvořit spravovanou doménu nebo [Tato nastavení definovat ručně][tutorial-create-instance-advanced]. V tomto kurzu se dozvíte, jak používat výchozí možnosti k vytvoření a konfiguraci instance služby Azure služba AD DS pomocí Azure Portal.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pochopení požadavků DNS pro spravovanou doménu
> * Vytvoření instance Azure AD DS
> * Povolení synchronizace hodnoty hash hesel

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Abyste mohli Azure služba AD DS povolit, potřebujete ve svém tenantovi Azure AD oprávnění *globálního správce* .
* Abyste mohli vytvořit požadované prostředky Azure služba AD DS, potřebujete oprávnění *přispěvatele* v předplatném Azure.

I když se pro Azure služba AD DS nevyžaduje, doporučuje se [nakonfigurovat Samoobslužné resetování hesla (SSPR)][configure-sspr] pro TENANTA Azure AD. Uživatelé si můžou změnit heslo bez SSPR, ale SSPR pomáhá, pokud si zapomene heslo a bude ho muset resetovat.

> [!IMPORTANT]
> Po vytvoření spravované domény Azure služba AD DS nemůžete instanci přesunout do jiné skupiny prostředků, virtuální sítě, předplatného atd. Při nasazování instance služby Azure služba AD DS je nutné vybrat nejvhodnější předplatné, skupinu prostředků, oblast a virtuální síť.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a nakonfigurujete instanci Azure služba AD DS pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="create-an-instance"></a>Vytvoření instance

Chcete-li spustit průvodce **povolením Azure AD Domain Services** , proveďte následující kroky:

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.
1. Do panelu hledání zadejte *Domain Services* a pak zvolte *Azure AD Domain Services* z návrhů hledání.
1. Na stránce Azure AD Domain Services vyberte **vytvořit**. Spustí se průvodce **povolením Azure AD Domain Services** .
1. Vyberte **předplatné** Azure, ve kterém chcete vytvořit spravovanou doménu.
1. Vyberte **skupinu prostředků** , do které má spravovaná doména patřit. Zvolte možnost **vytvořit novou** nebo vybrat existující skupinu prostředků.

Když vytváříte instanci Azure služba AD DS, zadáte název DNS. Při volbě tohoto názvu DNS máte nějaké okolnosti:

* **Název předdefinované domény:** Ve výchozím nastavení se používá integrovaný název domény adresáře (přípona *. onmicrosoft.com* ). Pokud chcete povolit přístup přes Internet k spravované doméně pomocí protokolu Secure LDAP, nemůžete vytvořit digitální certifikát pro zabezpečení připojení s touto výchozí doménou. Společnost Microsoft vlastní doménu *. onmicrosoft.com* , takže certifikační autorita (CA) certifikát nevydá.
* **Vlastní názvy domén:** Nejběžnějším přístupem je zadat vlastní název domény, obvykle ten, který už vlastníte a který je směrovatelný. Když použijete směrovatelný, vlastní doménu, může provoz správně přesměrovat podle potřeby na podporu vašich aplikací.
* **Přípony domén bez směrování:** Obecně doporučujeme, abyste se vyhnuli příponě názvu domény, která není směrovatelný, například *contoso. Local*. Přípona *. Local* není směrovatelný a může způsobit problémy s překladem názvů DNS.

> [!TIP]
> Pokud vytváříte vlastní název domény, je třeba dbát na stávající obory názvů DNS. Doporučuje se pro název domény zahrnout jedinečnou předponu. Například pokud je název vašeho kořenového adresáře DNS *contoso.com*, vytvořte spravovanou doménu Azure služba AD DS s vlastním názvem domény *Corp.contoso.com* nebo *DS.contoso.com*. V hybridním prostředí s místním prostředím služba AD DS se tyto předpony už můžou používat. Použijte jedinečnou předponu pro Azure služba AD DS.
>
> Můžete použít kořenový název DNS pro spravovanou doménu Azure služba AD DS, ale možná budete muset vytvořit další záznamy DNS pro další služby ve vašem prostředí. Pokud například spustíte webový server, který je hostitelem lokality pomocí kořenového názvu DNS, může dojít ke konfliktům názvů, které vyžadují další položky DNS.
>
> V těchto kurzech a v článcích s návody se jako krátký příklad používá vlastní doména *contoso.com* . Ve všech příkazech zadejte vlastní název domény, který může obsahovat jedinečnou předponu.
>
> Další informace najdete v tématu [Výběr předpony pro pojmenování pro doménu][naming-prefix].

Platí taky následující omezení názvů DNS:

* **Omezení prefixu domény:** Nelze vytvořit spravovanou doménu s předponou delší než 15 znaků. Předpona zadaného názvu domény (například *Contoso* v názvu domény *contoso.com* ) musí obsahovat maximálně 15 znaků.
* **Konflikty síťových názvů:** Název domény DNS pro spravovanou doménu už ve virtuální síti neexistuje. Konkrétně se podívejte na následující scénáře, které by mohly vést ke konfliktu názvů:
    * Pokud již máte doménu služby Active Directory se stejným názvem domény DNS ve službě Azure Virtual Network.
    * Pokud má virtuální síť, ve které plánujete povolit spravovanou doménu, připojení VPN s vaší místní sítí. V tomto scénáři se ujistěte, že nemáte doménu se stejným názvem domény DNS ve vaší místní síti.
    * Pokud máte existující cloudovou službu Azure s tímto názvem ve službě Azure Virtual Network.

Dokončete pole v okně *základy* Azure Portal a vytvořte instanci Azure služba AD DS:

1. Zadejte **název domény DNS** pro spravovanou doménu a vezměte v úvahu předchozí body.
1. Vyberte **umístění** Azure, ve kterém se má spravovaná doména vytvořit. Pokud zvolíte oblast, která podporuje Zóny dostupnosti, prostředky Azure služba AD DS se rozdělují mezi zóny, aby se mohla zvýšit redundance.

    Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Aby se zajistila odolnost, existuje minimálně tři samostatné zóny ve všech povolených oblastech.

    Není tu nic, co byste mohli nakonfigurovat pro Azure služba AD DS k distribuci mezi zónami. Platforma Azure automaticky zpracovává distribuci prostředků v zóně. Další informace a informace o dostupnosti oblastí najdete v tématu [co jsou zóny dostupnosti v Azure?][availability-zones]

    ![Konfigurace základního nastavení instance Azure AD Domain Services](./media/tutorial-create-instance/basics-window.png)

Pokud chcete rychle vytvořit spravovanou doménu Azure služba AD DS, můžete vybrat **zkontrolovat + vytvořit** a přijmout další výchozí možnosti konfigurace. Když zvolíte tuto možnost vytvoření, nakonfigurují se následující výchozí hodnoty:

* Vytvoří virtuální síť s názvem *aadds-VNet* , která používá rozsah IP adres *10.0.1.0/24*.
* Vytvoří podsíť s názvem *aadds-Subnet* pomocí rozsahu IP adres *10.0.1.0/24*.
* Synchronizuje *všechny* uživatele z Azure AD do spravované domény Azure služba AD DS.

1. Výběrem možnosti **zkontrolovat + vytvořit** přijměte tyto výchozí možnosti konfigurace.

## <a name="deploy-the-managed-domain"></a>Nasazení spravované domény

Na stránce **Souhrn** v průvodci zkontrolujte nastavení konfigurace pro spravovanou doménu. Chcete-li provést změny, můžete přejít zpět na libovolný krok průvodce. K opětovnému nasazení spravované domény Azure služba AD DS do jiného tenanta Azure AD pomocí těchto možností konfigurace můžete také **Stáhnout šablonu pro automatizaci**.

1. Pokud chcete vytvořit spravovanou doménu, vyberte **vytvořit**. Všimněte si, že po vytvoření spravovaného Azure služba AD DS se některé možnosti konfigurace, jako je třeba název DNS nebo virtuální síť, nedají změnit. Chcete-li pokračovat, vyberte **OK**.
1. Proces zřizování spravované domény může trvat až hodinu. Na portálu se zobrazí oznámení, ve kterém se zobrazuje průběh nasazení služby Azure služba AD DS. Vyberte oznámení, abyste viděli podrobný průběh nasazení.

    ![Oznámení v Azure Portal probíhajícího nasazení](./media/tutorial-create-instance/deployment-in-progress.png)

1. Stránka se načte s aktualizacemi v procesu nasazení, včetně vytváření nových prostředků ve vašem adresáři.
1. Vyberte skupinu prostředků, třeba *myResourceGroup*, a pak vyberte instanci Azure služba AD DS ze seznamu prostředků Azure, jako je třeba *contoso.com*. Na kartě **Přehled** se zobrazuje, že se spravovaná doména aktuálně *nasazuje*. Nemůžete nakonfigurovat spravovanou doménu, dokud není plně zřízené.

    ![Stav služby Domain Services ve stavu zřizování](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Když je spravovaná doména plně zřízena, karta **Přehled** zobrazuje stav domény jako *spuštěno*.

    ![Stav služby Domain Services po úspěšném zřízení](./media/tutorial-create-instance/successfully-provisioned.png)

Spravovaná doména je přidružená k vašemu tenantovi služby Azure AD. Během procesu zřizování vytvoří Azure služba AD DS dvě podnikové aplikace s názvem *Služba řadiče domény* a *AzureActiveDirectoryDomainControllerServices* v tenantovi Azure AD. Tyto podnikové aplikace jsou potřeba k obsluhování vaší spravované domény. Tyto aplikace neodstraňujte.

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

1. Přejděte na stránku přístupového panelu Azure AD na adrese [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. V pravém horním rohu vyberte své jméno a pak v rozevírací nabídce vyberte možnost **profil** .

    ![Výběr profilu](./media/tutorial-create-instance/select-profile.png)

1. Na stránce **profil** vyberte **změnit heslo**.
1. Na stránce **změnit heslo** zadejte stávající (staré) heslo a pak zadejte a potvrďte nové heslo.
1. Vyberte **Odeslat**.

Může to trvat několik minut, než se změní heslo pro nové heslo, aby bylo možné použít v Azure služba AD DS a úspěšně se přihlašujete k počítačům připojeným ke spravované doméně.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Pochopení požadavků DNS pro spravovanou doménu
> * Vytvoření instance Azure AD DS
> * Přidání uživatelů s právy pro správu do správy domén
> * Povolení uživatelských účtů pro Azure služba AD DS a generování hodnot hash hesel

Před virtuálními počítači připojenými k doméně a nasazením aplikací, které používají spravované domény Azure služba AD DS, nakonfigurujte pro úlohy aplikací službu Azure Virtual Network.

> [!div class="nextstepaction"]
> [Konfigurace služby Azure Virtual Network pro úlohy aplikací pro použití vaší spravované domény](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[availability-zones]: ../availability-zones/az-overview.md

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
