---
title: Kurz – vytvoření instance služby Azure Active Directory Domain Services | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak vytvořit a nakonfigurovat instanci služby Azure Active Directory Domain Services a zadat pokročilé možnosti konfigurace pomocí webu Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: f6817c1ec308e75a4af88825d46848b504775e19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239144"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>Kurz: Vytvoření a konfigurace instance služby Azure Active Directory Domain Services s rozšířenými možnostmi konfigurace

Služba Azure Active Directory Domain Services (Azure AD DS) poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, ověřování LDAP, Kerberos/NTLM, které je plně kompatibilní se službou Active Directory systému Windows Server. Tyto služby domény spotřebováváte bez nutnosti nasazovat, spravovat a opravovat řadiče domény sami. Azure AD DS integruje s vaším stávajícím tenantem Azure AD. Tato integrace umožňuje uživatelům přihlásit pomocí jejich podnikových přihlašovacích údajů a můžete použít existující skupiny a uživatelské účty k zabezpečení přístupu k prostředkům.

[Spravovanou doménu][tutorial-create-instance] můžete vytvořit pomocí výchozích možností konfigurace pro síť a synchronizaci nebo tato nastavení definovat ručně. Tento kurz ukazuje, jak definovat tyto pokročilé možnosti konfigurace k vytvoření a konfiguraci instance Azure AD DS pomocí portálu Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace nastavení DNS a virtuální sítě pro spravovanou doménu
> * Vytvoření instance Azure AD DS
> * Přidání administrativních uživatelů do správy domény
> * Povolení synchronizace hodnoty hash hesel

Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* K povolení služby Azure AD DS potřebujete oprávnění *globálního správce* ve vašem tenantovi Azure AD.
* K vytvoření požadovaných prostředků Azure AD DS potřebujete oprávnění *přispěvatele* ve vašem předplatném Azure.

I když není vyžadováno pro Azure AD DS, doporučuje se [nakonfigurovat samoobslužné resetování hesla (SSPR)][configure-sspr] pro klienta Azure AD. Uživatelé mohou změnit své heslo bez sspr, ale SSPR pomáhá, pokud zapomene své heslo a je třeba jej obnovit.

> [!IMPORTANT]
> Po vytvoření spravované domény Azure AD DS, nelze pak přesunout instanci do jiné skupiny prostředků, virtuální sítě, předplatného, atd. Při nasazení instance Azure AD DS vyberte nejvhodnější předplatné, skupinu prostředků, oblast a virtuální síť.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a nakonfigurujete instanci Azure AD DS pomocí portálu Azure. Chcete-li začít, nejprve se přihlaste na [portál Azure](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Vytvoření instance a konfigurace základních nastavení

Chcete-li spustit průvodce povolením průvodce **službou Azure AD Domain Services,** proveďte následující kroky:

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
1. Zadejte *domain services* do vyhledávacího panelu a z návrhů hledání zvolte *Služby domény Azure AD.*
1. Na stránce Služby domény Azure AD vyberte **Vytvořit**. Průvodce **povolením služby Azure AD Domain Services** je spuštěn.
1. Vyberte **předplatné** Azure, ve kterém chcete vytvořit spravovanou doménu.
1. Vyberte **skupinu Prostředků,** do které má spravovaná doména patřit. **Zvolte, zda** chcete vytvořit novou nebo vybrat existující skupinu prostředků.

Při vytváření instance Azure AD DS zadáte název DNS. Při výběru tohoto názvu DNS jsou některé důležité:

* **Vestavěné doménové jméno:** Ve výchozím nastavení se používá vestavěný název domény adresáře (přípona *.onmicrosoft.com).* Chcete-li povolit zabezpečený přístup LDAP ke spravované doméně přes Internet, nelze vytvořit digitální certifikát pro zabezpečení připojení k této výchozí doméně. Společnost Microsoft vlastní doménu *.onmicrosoft.com,* takže certifikační autorita (CA) certifikát nevystaví.
* **Vlastní názvy domén:** Nejběžnějším přístupem je zadání vlastního názvu domény, obvykle takového, který již vlastníte a je směrovatelný. Při použití směrovatelné, vlastní domény, provoz může správně toku podle potřeby pro podporu aplikací.
* **Nesměrovatelné přípony domény:** Obecně doporučujeme vyhnout se nesměrovatelné příponě názvu domény, například *contoso.local*. Přípona *LOCAL* není směrovatelná a může způsobit problémy s rozlišením DNS.

> [!TIP]
> Pokud vytvoříte vlastní název domény, dbát na existující obory názvů DNS. Doporučujeme používat název domény oddělený od existujícího oboru názvů Azure nebo místního webu DNS.
>
> Pokud například máte existující obor názvů DNS *contoso.com*, vytvořte spravovanou doménu Služby Azure AD DS s vlastním názvem domény *aaddscontoso.com*. Pokud potřebujete použít zabezpečené protokol LDAP, musíte se zaregistrovat a vlastnit tento vlastní název domény, abyste vygenerovali požadované certifikáty.
>
> Možná budete muset vytvořit další záznamy DNS pro jiné služby ve vašem prostředí nebo podmíněné servery pro předávání DNS mezi existujícími obory názvů DNS ve vašem prostředí. Pokud například spustíte webový server, který hostuje web pomocí kořenového názvu DNS, mohou existovat konflikty názvů, které vyžadují další položky DNS.
>
> V těchto kurzech a články s postupy vlastní doména *aaddscontoso.com* se používá jako krátký příklad. Ve všech příkazech zadejte vlastní název domény.

Platí také následující omezení názvů DNS:

* **Omezení předpony domény:** Spravovanou doménu s předponou delší než 15 znaků nelze vytvořit. Předpona zadaného názvu domény (například *aaddscontoso* v *aaddscontoso.com* názvu domény) musí obsahovat 15 nebo méně znaků.
* **Konflikty názvů sítí:** Název domény DNS pro spravovanou doménu by už neměl ve virtuální síti existovat. Konkrétně zkontrolujte následující scénáře, které by vedly ke konfliktu názvů:
    * Pokud už máte doménu služby Active Directory se stejným názvem domény DNS ve virtuální síti Azure.
    * Pokud virtuální síť, ve které plánujete povolit spravovanou doménu, má připojení VPN k místní síti. V tomto scénáři ujistěte se, že nemáte doménu se stejným názvem domény DNS v místní síti.
    * Pokud máte existující cloudovou službu Azure s tímto názvem ve virtuální síti Azure.

Dokončete pole v okně *Základy* na webu Azure Portal a vytvořte instanci Služby Azure AD DS:

1. Zadejte **název domény DNS** pro spravovanou doménu s přihlédnutím k předchozím bodům.
1. Zvolte **umístění Azure,** ve kterém má být vytvořena spravovaná doména. Pokud zvolíte oblast, která podporuje zóny dostupnosti, prostředky Azure AD DS jsou distribuovány napříč zónami pro další redundanci.

    Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Aby byla zajištěna odolnost proti chybám, jsou ve všech povolených oblastech minimálně tři samostatné zóny.

    Není nic pro vás nakonfigurovat pro Azure AD DS distribuovat napříč zónami. Platforma Azure automaticky zpracovává zónovou distribuci prostředků. Další informace a dostupnost oblasti najdete v tématu [Co jsou zóny dostupnosti v Azure?][availability-zones]

1. Skladová **položka** určuje výkon, frekvenci zálohování a maximální počet vztahů důvěryhodnosti doménové struktury, které můžete vytvořit. Skladovou položku můžete změnit po vytvoření spravované domény, pokud se změní vaše obchodní požadavky nebo požadavky. Další informace naleznete v [tématu koncepty skladových položk služby Azure AD DS][concepts-sku].

    Pro účely tohoto kurzu vyberte *standardní* skladovou položku.
1. *Doménová struktura* je logická konstrukce používaná službou Active Directory Domain Services k seskupení jedné nebo více domén. Ve výchozím nastavení se spravovaná doména Azure AD DS vytvoří jako doménová struktura *uživatele.* Tento typ doménové struktury synchronizuje všechny objekty ze služby Azure AD, včetně všech uživatelských účtů vytvořených v místním prostředí služby AD DS. Doménová struktura *prostředků* synchronizuje jenom uživatele a skupiny vytvořené přímo ve službě Azure AD. Doménové struktury prostředků jsou aktuálně ve verzi Preview. Další informace o doménových strukturách *prostředků,* včetně toho, proč můžete použít jeden a jak vytvořit vztahy důvěryhodnosti doménové struktury s místními doménami služby AD DS, najdete v [tématu Přehled doménových struktur prostředků Služby Azure AD DS][resource-forests].

    V tomto kurzu zvolte vytvoření doménové struktury *uživatele.*

    ![Konfigurace základního nastavení instance služby Azure AD Domain Services](./media/tutorial-create-instance-advanced/basics-window.png)

1. Chcete-li ručně nakonfigurovat další možnosti, zvolte **Další – síť**. V opačném případě vyberte **možnost Revize + vytvořit,** chcete-li přijmout výchozí možnosti konfigurace, přejděte do oddílu [a nasadit spravovanou doménu](#deploy-the-managed-domain). Následující výchozí hodnoty jsou konfigurovány, když zvolíte tuto možnost vytvořit:

    * Vytvoří virtuální síť s názvem *aadds-vnet,* která používá rozsah IP adres *10.0.1.0/24*.
    * Vytvoří podsíť s názvem *aadds-podsíť* pomocí rozsahu IP adres *10.0.1.0/24*.
    * Synchronizuje *všechny* uživatele z Azure AD do spravované domény Azure AD DS.

## <a name="create-and-configure-the-virtual-network"></a>Vytvoření a konfigurace virtuální sítě

K zajištění připojení je potřeba virtuální síť Azure a vyhrazená podsíť. Azure AD DS je povolená v této podsíti virtuální sítě. V tomto kurzu vytvoříte virtuální síť, i když můžete místo toho zvolit použití existující virtuální sítě. V obou přístupech je nutné vytvořit vyhrazenou podsíť pro použití službou Azure AD DS.

Některé aspekty této vyhrazené podsítě virtuální sítě zahrnují následující oblasti:

* Podsíť musí mít alespoň 3-5 k dispozici IP adresy ve svém rozsahu adres pro podporu prostředků Azure AD DS.
* Nevybírejte podsíť *Brány* pro nasazení služby Azure AD DS. Není podporována nasazení Azure AD DS do podsítě *brány.*
* Nenasazujte do podsítě žádné jiné virtuální počítače. Aplikace a virtuální aplikace často používají skupiny zabezpečení sítě k zabezpečení připojení. Spuštění těchto úloh v samostatné podsíti umožňuje použít tyto skupiny zabezpečení sítě bez narušení připojení ke spravované doméně.
* Po povolení služby Azure AD DS nelze přesunout spravovanou doménu do jiné virtuální sítě.

Další informace o plánování a konfiguraci virtuální sítě naleznete v [tématu důležité informace o sítích pro službu Azure Active Directory Domain Services][network-considerations].

Dokončete pole v okně *Síť* takto:

1. Na stránce **Síť** zvolte virtuální síť, do které chcete nasadit Službu Azure AD DS, z rozevírací nabídky nebo vyberte **Vytvořit nový**.
    1. Pokud se rozhodnete vytvořit virtuální síť, zadejte název virtuální sítě, například *myVnet*, zadejte rozsah adres, například *10.0.1.0/24*.
    1. Vytvořte vyhrazenou podsíť s jasným názvem, například *DomainServices*. Zadejte rozsah adres, například *10.0.1.0/24*.

    [![](./media/tutorial-create-instance-advanced/create-vnet.png "Create a virtual network and subnet for use with Azure AD Domain Services")](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Ujistěte se, že jste vybrali rozsah adres, který je v rozsahu vašich privátních IP adres. Rozsahy IP adres, které nevlastníte, které jsou v prostoru veřejné adresy, způsobují chyby v rámci služby Azure AD DS.

1. Vyberte podsíť virtuální sítě, například *DomainServices*.
1. Až budete připraveni, zvolte **Další - Správa**.

## <a name="configure-an-administrative-group"></a>Konfigurace skupiny pro správu

Speciální skupina pro správu s názvem *Správci řadiče domény AAD* se používá pro správu domény Azure AD DS. Členům této skupiny jsou udělena oprávnění správce virtuálních počítačů, které jsou připojeny ke spravované doméně. Na virtuálních počítačích spojených s doménou se tato skupina přidá do místní skupiny administrators. Členové této skupiny se také můžou vzdáleně připojovat ke virtuálním počítačům spojeným s doménou.

Nemáte oprávnění *správce domény* nebo *podnikového správce* ve spravované doméně pomocí služby Azure AD DS. Tato oprávnění jsou vyhrazena službou a nejsou k dispozici uživatelům v rámci klienta. Místo toho skupina *Správci řadiče domény aadu aaditi* umožňuje provádět některé privilegované operace. Tyto operace zahrnují příslušnost do skupiny pro správu na virtuálních počítačích spojených s doménami a konfiguraci zásad skupiny.

Průvodce automaticky vytvoří skupinu *Správců řadiče domény AAD* ve vašem adresáři Azure AD. Pokud máte existující skupinu s tímto názvem v adresáři Azure AD, průvodce vybere tuto skupinu. Volitelně můžete během procesu nasazení přidat další uživatele do této *skupiny správců řadiče domény aaduD.* Tyto kroky lze provést později.

1. Chcete-li do této *skupiny Administrators řadiče domény aadu Přidat* další uživatele, vyberte **možnost Spravovat členství ve skupině**.

    ![Konfigurace členství ve skupině Správci řadiče domény aadu AAD](./media/tutorial-create-instance-advanced/admin-group.png)

1. Vyberte tlačítko **Přidat členy** a pak vyhledejte a vyberte uživatele z adresáře Azure AD. Vyhledejte například svůj vlastní účet a přidejte jej do *skupiny Správci řadiče domény řadiče domény AAD.*
1. V případě potřeby změňte nebo přidejte další příjemce pro oznámení, pokud jsou výstrahy ve spravované doméně Azure AD DS, které vyžadují pozornost.
1. Až budete připraveni, zvolte **Další – synchronizace**.

## <a name="configure-synchronization"></a>Konfigurace synchronizace

Azure AD DS umožňuje synchronizovat *všechny* uživatele a skupiny dostupné ve službě Azure AD nebo synchronizaci s *vymezeným oborem* pouze konkrétní skupiny. Pokud se rozhodnete synchronizovat *všechny* uživatele a skupiny, nemůžete později zvolit pouze synchronizaci s vymezeným oborem. Další informace o synchronizaci s vymezeným oborem naleznete v tématu [Synchronizace s oborem služby Azure AD Domain Services][scoped-sync].

1. V tomto kurzu zvolte synchronizaci **Všech** uživatelů a skupin. Tato volba synchronizace je výchozí možností.

    ![Provedení úplné synchronizace uživatelů a skupin z Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Vyberte **Zkontrolovat a vytvořit**.

## <a name="deploy-the-managed-domain"></a>Nasazení spravované domény

Na stránce **Souhrn** průvodce zkontrolujte nastavení konfigurace spravované domény. Chcete-li provést změny, můžete se vrátit k libovolnému kroku průvodce. Chcete-li znovu nasadit spravovanou doménu Azure AD DS do jiného klienta Azure AD konzistentním způsobem pomocí těchto možností konfigurace, můžete si také **stáhnout šablonu pro automatizaci**.

1. Chcete-li vytvořit spravovanou doménu, vyberte **vytvořit**. Zobrazí se poznámka, že některé možnosti konfigurace, jako je název DNS nebo virtuální síť, nelze změnit, jakmile byla vytvořena spravovaná služba Azure AD DS. Chcete-li pokračovat, vyberte **možnost OK**.
1. Proces zřizování spravované domény může trvat až hodinu. Na portálu se zobrazí oznámení, které zobrazuje průběh nasazení služby Azure AD DS. Výběrem oznámení zobrazíte podrobný průběh nasazení.

    ![Oznámení probíhajícího nasazení na portálu Azure](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Vyberte skupinu prostředků, jako je *myResourceGroup*, a pak zvolte instanci Azure AD DS ze seznamu prostředků Azure, jako je *aaddscontoso.com*. Karta **Přehled** ukazuje, že spravovaná doména právě *nasazuje*. Spravovanou doménu nelze nakonfigurovat, dokud nebude plně zřízena.

    ![Stav služby Domain Services během stavu zřizování](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Když je spravovaná doména plně zřízená, karta **Přehled** zobrazuje stav domény jako *Spuštěno*.

    ![Stav služby Domény po úspěšném zřízení](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

Spravovaná doména je přidružená k vašemu tenantovi Azure AD. Během procesu zřizování Azure AD DS vytvoří dvě podnikové aplikace s názvem *Služby řadiče domény* a *Služby AzureActiveDirectoryDomainControllerServices* v tenantovi Azure AD. Tyto podnikové aplikace jsou potřebné k provozu spravované domény. Tyto aplikace neodstraňujte.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Aktualizace nastavení DNS pro virtuální síť Azure

S Azure AD DS úspěšně nasazené, teď nakonfigurujte virtuální síť tak, aby ostatní připojené virtuální počítače a aplikace používat spravovanou doménu. Chcete-li toto připojení poskytnout, aktualizujte nastavení serveru DNS pro vaši virtuální síť tak, aby ukazovalo na dvě IP adresy, kde se nasazuje Služba Azure AD DS.

1. Karta **Přehled** spravované domény zobrazuje některé **požadované kroky konfigurace**. Prvním krokem konfigurace je aktualizace nastavení serveru DNS pro vaši virtuální síť. Po správné konfiguraci nastavení DNS se tento krok již nezobrazuje.

    Uvedené adresy jsou řadiče domény pro použití ve virtuální síti. V tomto příkladu jsou tyto adresy *10.1.0.4* a *10.1.0.5*. Tyto ADRESY IP později najdete na kartě **Vlastnosti.**

    ![Konfigurace nastavení DNS pro vaši virtuální síť pomocí IP adres Služby Azure AD Domain Services](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Chcete-li aktualizovat nastavení serveru DNS pro virtuální síť, vyberte tlačítko **Konfigurovat.** Nastavení DNS se automaticky nakonfiguruje pro vaši virtuální síť.

> [!TIP]
> Pokud jste v předchozích krocích vybrali existující virtuální síť, všechny virtuální počítače připojené k síti získají po restartování pouze nové nastavení DNS. Virtuální počítače můžete restartovat pomocí portálu Azure, Azure PowerShellu nebo azure cli.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Povolení uživatelských účtů pro Azure AD DS

K ověření uživatelů ve spravované doméně potřebuje služba Azure AD DS hodnotu hash hesel ve formátu, který je vhodný pro nt lan manager (NTLM) a ověřování protokolem Kerberos. Azure AD negeneruje ani ukládat hesla hashe ve formátu, který je vyžadován pro ověřování NTLM nebo Kerberos, dokud nepovolíte Azure AD DS pro vašeho tenanta. Z bezpečnostních důvodů Azure AD také neukládá žádné přihlašovací údaje hesla ve formě prostého textu. Azure AD proto nemůže automaticky generovat tyto hashe ntlm nebo kerberos hesla na základě existujících přihlašovacích údajů uživatelů.

> [!NOTE]
> Po vhodně nakonfigurované, použitelné heslo hashes jsou uloženy ve spravované doméně Azure AD DS. Pokud odstraníte spravovanou doménu Azure AD DS, odstraní se také všechny hashe hesel uložené v tomto okamžiku. Informace o synchronizovaných přihlašovacích údajích ve službě Azure AD nelze znovu použít, pokud později vytvoříte spravovanou doménu Azure AD DS – je nutné znovu nakonfigurovat synchronizaci hodnot hash hesla, abyste znovu ukládali hodnotové hodnotu hash hesel. Dříve virtuální počítače spojené s doménou nebo uživatelé nebudou moct okamžitě ověřit – Azure AD potřebuje generovat a ukládat hash hesla v nové spravované doméně Azure AD DS. Další informace najdete [v tématu proces synchronizace hash hesla pro Azure AD DS a Azure AD Connect][password-hash-sync-process].

Kroky generování a ukládání těchto hesel hash se liší pro uživatelské účty pouze pro cloud vytvořené ve službě Azure AD oproti uživatelským účtům, které jsou synchronizovány z místního adresáře pomocí služby Azure AD Connect. Uživatelský účet jenom cloudu je účet vytvořený v adresáři služby Azure AD pomocí webu Azure Portal nebo rutin Azure AD PowerShellu. Tyto uživatelské účty nejsou synchronizovány z místního adresáře. V tomto kurzu pojďme pracovat se základním uživatelským účtem pouze pro cloud. Další informace o dalších krocích, které jsou nutné k používání služby Azure AD Connect, najdete [v tématu Synchronizace hashe hesel pro uživatelské účty synchronizované z místní služby AD do spravované domény][on-prem-sync].

> [!TIP]
> Pokud váš klient Azure AD obsahuje kombinaci uživatelů a uživatelů pouze pro cloud z vašeho místního ad, je třeba provést obě sady kroků.

Pro uživatelské účty pouze pro cloud uživatelé musí změnit svá hesla, než budou moci používat Azure AD DS. Tento proces změny hesla způsobí, že hodnot hash hesel pro ověřování protokolů Kerberos a NTLM se vygenerují a uloží ve službě Azure AD. Můžete buď vyprší platnost hesla pro všechny uživatele v tenantovi, kteří potřebují používat Azure AD DS, který vynutí změnu hesla při příštím přihlášení, nebo pokyn, aby ručně změnit svá hesla. V tomto kurzu ručně změníme uživatelské heslo.

Než bude uživatel moci resetovat své heslo, musí být klient Azure AD [nakonfigurovaný pro samoobslužné resetování hesla][configure-sspr].

Chcete-li změnit heslo pro uživatele pouze v cloudu, musí uživatel provést následující kroky:

1. Přejděte na stránku přístupového [https://myapps.microsoft.com](https://myapps.microsoft.com)panelu Azure AD na adrese .
1. V pravém horním rohu vyberte své jméno a v rozevírací nabídce zvolte **Profil.**

    ![Výběr profilu](./media/tutorial-create-instance-advanced/select-profile.png)

1. Na stránce **Profil** vyberte **Změnit heslo**.
1. Na stránce **Změnit heslo** zadejte stávající (staré) heslo a potvrďte nové heslo.
1. Vyberte **Odeslat**.

Trvá několik minut poté, co jste změnili heslo pro nové heslo, které mají být použitelné v Azure AD DS a úspěšně přihlásit k počítačům připojen ke spravované doméně.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace nastavení DNS a virtuální sítě pro spravovanou doménu
> * Vytvoření instance Azure AD DS
> * Přidání administrativních uživatelů do správy domény
> * Povolení uživatelských účtů pro Azure AD DS a generování hesel hash

Chcete-li zobrazit tuto spravovanou doménu v akci, vytvořte a připojte virtuální počítač k doméně.

> [!div class="nextstepaction"]
> [Připojení virtuálního počítače se systémem Windows Server ke spravované doméně](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
