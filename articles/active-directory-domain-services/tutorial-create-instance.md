---
title: Kurz – vytvoření spravované domény Azure Active Directory Domain Services | Microsoft Docs
description: V tomto kurzu se naučíte, jak vytvořit a nakonfigurovat Azure Active Directory Domain Services spravovanou doménu pomocí Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: dc225cef2981bd6307a919d52b75d3e0791e46cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87490996"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain"></a>Kurz: vytvoření a konfigurace spravované domény Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP, ověřování Kerberos/NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory. Tyto doménové služby spotřebujete bez nutnosti nasazovat, spravovat a opravovat řadiče domény sami. Služba Azure služba AD DS se integruje s vaším stávajícím tenant Azure AD. Tato integrace umožňuje uživatelům přihlásit se pomocí svých podnikových přihlašovacích údajů a pomocí existujících skupin a uživatelských účtů můžete zabezpečit přístup k prostředkům.

Pomocí výchozích možností konfigurace pro sítě a synchronizaci můžete vytvořit spravovanou doménu nebo [Tato nastavení definovat ručně][tutorial-create-instance-advanced]. V tomto kurzu se dozvíte, jak používat výchozí možnosti k vytvoření a konfiguraci spravované domény Azure služba AD DS pomocí Azure Portal.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pochopení požadavků DNS pro spravovanou doménu
> * Vytvoření spravované domény
> * Povolení synchronizace hodnoty hash hesel

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Abyste mohli Azure služba AD DS povolit, potřebujete ve svém tenantovi Azure AD oprávnění *globálního správce* .
* Abyste mohli vytvořit požadované prostředky Azure služba AD DS, potřebujete oprávnění *přispěvatele* v předplatném Azure.

I když se pro Azure služba AD DS nevyžaduje, doporučuje se [nakonfigurovat Samoobslužné resetování hesla (SSPR)][configure-sspr] pro TENANTA Azure AD. Uživatelé si můžou změnit heslo bez SSPR, ale SSPR pomáhá, pokud si zapomene heslo a bude ho muset resetovat.

> [!IMPORTANT]
> Po vytvoření spravované domény nemůžete tuto spravovanou doménu přesunout do jiné skupiny prostředků, virtuální sítě, předplatného atd. Při nasazení spravované domény se ujistěte, že vyberete nejvhodnější předplatné, skupinu prostředků, oblast a virtuální síť.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a nakonfigurujete spravovanou doménu pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="create-a-managed-domain"></a>Vytvoření spravované domény

Chcete-li spustit průvodce **povolením Azure AD Domain Services** , proveďte následující kroky:

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
1. Do panelu hledání zadejte *Domain Services* a pak zvolte *Azure AD Domain Services* z návrhů hledání.
1. Na stránce Azure AD Domain Services vyberte **vytvořit**. Spustí se průvodce **povolením Azure AD Domain Services** .
1. Vyberte **předplatné** Azure, ve kterém chcete vytvořit spravovanou doménu.
1. Vyberte **skupinu prostředků** , do které má spravovaná doména patřit. Zvolte možnost **vytvořit novou** nebo vybrat existující skupinu prostředků.

Při vytváření spravované domény zadáte název DNS. Při volbě tohoto názvu DNS máte nějaké okolnosti:

* **Název předdefinované domény:** Ve výchozím nastavení se používá integrovaný název domény adresáře (přípona *. onmicrosoft.com* ). Pokud chcete povolit přístup přes Internet k spravované doméně pomocí protokolu Secure LDAP, nemůžete vytvořit digitální certifikát pro zabezpečení připojení s touto výchozí doménou. Společnost Microsoft vlastní doménu *. onmicrosoft.com* , takže certifikační autorita (CA) certifikát nevydá.
* **Vlastní názvy domén:** Nejběžnějším přístupem je zadat vlastní název domény, obvykle ten, který už vlastníte a který je směrovatelný. Když použijete směrovatelný, vlastní doménu, může provoz správně přesměrovat podle potřeby na podporu vašich aplikací.
* **Přípony domén bez směrování:** Obecně doporučujeme, abyste se vyhnuli příponě názvu domény, která není směrovatelný, například *contoso. Local*. Přípona *. Local* není směrovatelný a může způsobit problémy s překladem názvů DNS.

> [!TIP]
> Pokud vytváříte vlastní název domény, je třeba dbát na stávající obory názvů DNS. Doporučuje se použít název domény oddělený od existujícího prostoru názvů Azure nebo místního DNS.
>
> Pokud máte například existující obor názvů DNS *contoso.com*, vytvořte spravovanou doménu s vlastním názvem domény *aaddscontoso.com*. Pokud potřebujete použít zabezpečený protokol LDAP, musíte tento vlastní název domény pro vygenerování požadovaných certifikátů zaregistrovat a vlastnit.
>
> Možná budete muset vytvořit některé další záznamy DNS pro další služby ve vašem prostředí nebo podmíněné služby DNS pro přeposílání mezi stávajícími obory názvů DNS ve vašem prostředí. Pokud například spustíte webový server, který je hostitelem lokality pomocí kořenového názvu DNS, může dojít ke konfliktům názvů, které vyžadují další položky DNS.
>
> V těchto kurzech a v článcích s návody se jako krátký příklad používá vlastní doména *aaddscontoso.com* . Ve všech příkazech zadejte vlastní název domény.

Platí taky následující omezení názvů DNS:

* **Omezení prefixu domény:** Nelze vytvořit spravovanou doménu s předponou delší než 15 znaků. Předpona zadaného názvu domény (například *aaddscontoso* v názvu domény *aaddscontoso.com* ) musí obsahovat nejvýše 15 znaků.
* **Konflikty síťových názvů:** Název domény DNS pro spravovanou doménu už ve virtuální síti neexistuje. Konkrétně se podívejte na následující scénáře, které by mohly vést ke konfliktu názvů:
    * Pokud již máte doménu služby Active Directory se stejným názvem domény DNS ve službě Azure Virtual Network.
    * Pokud má virtuální síť, ve které plánujete povolit spravovanou doménu, připojení VPN s vaší místní sítí. V tomto scénáři se ujistěte, že nemáte doménu se stejným názvem domény DNS ve vaší místní síti.
    * Pokud máte existující cloudovou službu Azure s tímto názvem ve službě Azure Virtual Network.

Vyplňte pole v okně *základy* Azure Portal k vytvoření spravované domény:

1. Zadejte **název domény DNS** pro spravovanou doménu a vezměte v úvahu předchozí body.
1. Vyberte **umístění** Azure, ve kterém se má spravovaná doména vytvořit. Pokud zvolíte oblast, která podporuje Zóny dostupnosti Azure, prostředky Azure služba AD DS se rozdělují mezi zóny, aby se mohla zvýšit redundance.

    > [!TIP]
    > Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Kvůli odolnosti ve všech aktivovaných oblastech existují minimálně tři samostatné zóny.
    >
    > Není tu nic, co byste mohli nakonfigurovat pro Azure služba AD DS k distribuci mezi zónami. Platforma Azure automaticky zpracovává distribuci prostředků v zóně. Další informace a informace o dostupnosti oblastí najdete v tématu [co jsou zóny dostupnosti v Azure?][availability-zones]

1. **SKU** určuje výkon, četnost záloh a maximální počet vztahů důvěryhodnosti doménové struktury, které můžete vytvořit. SKU můžete změnit po vytvoření spravované domény, pokud vaše obchodní požadavky nebo požadavky budou změněny. Další informace najdete v tématu [Koncepty služby Azure služba AD DS SKU][concepts-sku].

    Pro tento kurz vyberte *standardní* SKU.
1. *Doménová struktura* je logická konstrukce, kterou používá Active Directory Domain Services k seskupení jedné nebo více domén. Ve výchozím nastavení je spravovaná doména vytvořena jako doménová struktura *uživatelů* . Tento typ doménové struktury synchronizuje všechny objekty z Azure AD, včetně všech uživatelských účtů vytvořených v místním služba AD DS prostředí.

    Doménová struktura *prostředků* synchronizuje jenom uživatele a skupiny vytvořené přímo ve službě Azure AD. Další informace o doménových strukturách *prostředků* , včetně důvodů, proč je můžete použít a jak vytvořit vztahy důvěryhodnosti doménové struktury s místními služba AD DS doménami, najdete v tématu [Přehled doménových struktur Azure služba AD DS][resource-forests].

    V tomto kurzu se rozhodnete vytvořit doménovou strukturu *uživatelů* .

    ![Konfigurace základního nastavení pro Azure AD Domain Services spravovanou doménu](./media/tutorial-create-instance/basics-window.png)

Pokud chcete rychle vytvořit spravovanou doménu, můžete vybrat **zkontrolovat + vytvořit** a přijmout další výchozí možnosti konfigurace. Když zvolíte tuto možnost vytvoření, nakonfigurují se následující výchozí hodnoty:

* Vytvoří virtuální síť s názvem *aadds-VNet* , která používá rozsah IP adres *10.0.2.0/24*.
* Vytvoří podsíť s názvem *aadds-Subnet* pomocí rozsahu IP adres *10.0.2.0/24*.
* Synchronizuje *všechny* uživatele ze služby Azure AD do spravované domény.

Výběrem možnosti **zkontrolovat + vytvořit** přijměte tyto výchozí možnosti konfigurace.

## <a name="deploy-the-managed-domain"></a>Nasazení spravované domény

Na stránce **Souhrn** v průvodci zkontrolujte nastavení konfigurace vaší spravované domény. Chcete-li provést změny, můžete přejít zpět na libovolný krok průvodce. Chcete-li znovu nasadit spravovanou doménu do jiného tenanta Azure AD pomocí těchto možností konfigurace, můžete také **Stáhnout šablonu pro automatizaci**.

1. Pokud chcete vytvořit spravovanou doménu, vyberte **vytvořit**. Všimněte si, že po vytvoření spravovaného Azure služba AD DS se některé možnosti konfigurace, jako je třeba název DNS nebo virtuální síť, nedají změnit. Chcete-li pokračovat, vyberte **OK**.
1. Proces zřizování spravované domény může trvat až hodinu. Na portálu se zobrazí oznámení, ve kterém se zobrazuje průběh nasazení služby Azure služba AD DS. Vyberte oznámení, abyste viděli podrobný průběh nasazení.

    ![Oznámení v Azure Portal probíhajícího nasazení](./media/tutorial-create-instance/deployment-in-progress.png)

1. Stránka se načte s aktualizacemi v procesu nasazení, včetně vytváření nových prostředků ve vašem adresáři.
1. Vyberte skupinu prostředků, třeba *myResourceGroup*, a pak vyberte spravovanou doménu ze seznamu prostředků Azure, jako je třeba *aaddscontoso.com*. Na kartě **Přehled** se zobrazuje, že se spravovaná doména aktuálně *nasazuje*. Nemůžete nakonfigurovat spravovanou doménu, dokud není plně zřízené.

    ![Stav služby Domain Services ve stavu zřizování](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Když je spravovaná doména plně zřízena, karta **Přehled** zobrazuje stav domény jako *spuštěno*.

    ![Stav služby Domain Services po úspěšném zřízení](./media/tutorial-create-instance/successfully-provisioned.png)

> [!IMPORTANT]
> Spravovaná doména je přidružená k vašemu tenantovi služby Azure AD. Během procesu zřizování vytvoří Azure služba AD DS dvě podnikové aplikace s názvem *Služba řadiče domény* a *AzureActiveDirectoryDomainControllerServices* v tenantovi Azure AD. Tyto podnikové aplikace jsou potřeba k obsluhování vaší spravované domény. Tyto aplikace neodstraňujte.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Aktualizace nastavení DNS pro virtuální síť Azure

Po úspěšném nasazení Azure služba AD DS nyní nakonfigurujte virtuální síť tak, aby povolovala jiným připojeným virtuálním počítačům a aplikacím používat spravovanou doménu. Pokud chcete toto připojení poskytnout, aktualizujte nastavení serveru DNS virtuální sítě tak, aby odkazovalo na dvě IP adresy, na kterých je spravovaná doména nasazená.

1. Karta **Přehled** pro spravovanou doménu zobrazuje některé **požadované kroky konfigurace**. První krok konfigurace je aktualizovat nastavení serveru DNS pro vaši virtuální síť. Po správné konfiguraci nastavení DNS se tento krok už nezobrazuje.

    Uvedené adresy jsou řadiče domény pro použití ve virtuální síti. V tomto příkladu jsou tyto adresy *10.0.2.4* a *10.0.2.5*. Tyto IP adresy můžete později najít na kartě **vlastnosti** .

    ![Konfigurace nastavení DNS pro vaši virtuální síť s Azure AD Domain Services IP adresami](./media/tutorial-create-instance/configure-dns.png)

1. Pokud chcete aktualizovat nastavení serveru DNS pro virtuální síť, klikněte na tlačítko **Konfigurovat** . Nastavení DNS se automaticky nakonfigurují pro vaši virtuální síť.

> [!TIP]
> Pokud jste v předchozích krocích vybrali existující virtuální síť, všechny virtuální počítače připojené k síti získají po restartování jenom nové nastavení DNS. Virtuální počítače můžete restartovat pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Povolení uživatelských účtů pro Azure služba AD DS

K ověřování uživatelů ve spravované doméně služba AD DS Azure potřebuje hodnoty hash hesel ve formátu, který je vhodný pro ověřování pomocí protokolu NTLM (NT LAN Manager) a Kerberos. Azure AD negeneruje nebo ukládá hodnoty hash hesel ve formátu, který je vyžadován pro ověřování protokolem NTLM nebo Kerberos, dokud nepovolíte služba AD DS Azure pro vašeho tenanta. Z bezpečnostních důvodů Azure AD také neukládá přihlašovací údaje hesla ve formě nešifrovaných textů. Proto služba Azure AD nemůže automaticky generovat tyto hodnoty hash hesla NTLM nebo Kerberos na základě stávajících přihlašovacích údajů uživatelů.

> [!NOTE]
> Po správné konfiguraci budou použitelné hodnoty hash hesel uloženy ve spravované doméně. Pokud odstraníte spravovanou doménu, odstraní se také všechny hodnoty hash hesel uložené v tomto okamžiku.
>
> Informace o synchronizovaných přihlašovacích údajích ve službě Azure AD se nedají znovu použít, pokud později vytvoříte spravovanou doménu – synchronizace hodnot hash hesel se znovu uloží na znovu. Virtuální počítače připojené k doméně nebo uživatelé nebudou moct hned ověřit – Azure AD potřebuje vygenerovat a uložit hodnoty hash hesel v nové spravované doméně.
>
> Další informace najdete v tématu [proces synchronizace hodnot hash hesel pro Azure služba AD DS a Azure AD Connect][password-hash-sync-process].

Postup generování a ukládání hodnot hash hesel se liší pro uživatelské účty, které jsou vytvořené v Azure AD, oproti uživatelským účtům, které jsou synchronizované z místního adresáře pomocí Azure AD Connect.

Uživatelský účet jenom cloudu je účet vytvořený v adresáři služby Azure AD pomocí webu Azure Portal nebo rutin Azure AD PowerShellu. Tyto uživatelské účty se nesynchronizují z místního adresáře.

> V tomto kurzu budeme pracovat se základním uživatelským účtem jenom pro Cloud. Další informace o dalších krocích potřebných pro použití Azure AD Connect najdete v tématu [synchronizace hodnot hash hesel u uživatelských účtů synchronizovaných z místní služby AD do spravované domény][on-prem-sync].

> [!TIP]
> Pokud má tenant služby Azure AD kombinaci uživatelů jenom pro Cloud a uživatelů z místní služby AD, musíte dokončit obě sady kroků.

U uživatelských účtů jenom pro Cloud musí uživatelé změnit svoje heslo, aby mohli používat Azure služba AD DS. Tento proces změny hesla způsobí, že se ve službě Azure AD vygenerují a ukládají hodnoty hash hesel pro ověřování pomocí protokolu Kerberos a NTLM. Účet není synchronizovaný z Azure AD do Azure služba AD DS, dokud se nezmění heslo. Buď vyprší platnost hesel pro všechny uživatele cloudu v tenantovi, kteří potřebují používat Azure služba AD DS, což vynutí změnu hesla při příštím přihlašování, nebo dá uživatelům v cloudu pokyn, aby ručně změnili hesla. V tomto kurzu ručně změníte heslo uživatele.

Než bude moct uživatel resetovat heslo, musí být tenant služby Azure AD [nakonfigurovaný pro Samoobslužné resetování hesla][configure-sspr].

Chcete-li změnit heslo pouze pro cloudového uživatele, musí uživatel provést následující kroky:

1. Přejděte na stránku přístupového panelu Azure AD na adrese [https://myapps.microsoft.com](https://myapps.microsoft.com) .
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
> * Vytvoření spravované domény
> * Přidání uživatelů s právy pro správu do správy domén
> * Povolení uživatelských účtů pro Azure služba AD DS a generování hodnot hash hesel

Před virtuálními počítači připojenými k doméně a nasazením aplikací, které používají spravovanou doménu, nakonfigurujte pro úlohy aplikací službu Azure Virtual Network.

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
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
