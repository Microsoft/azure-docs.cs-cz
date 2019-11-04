---
title: Připojení virtuálního počítače s Windows serverem ke spravované doméně | Microsoft Docs
description: V tomto kurzu se dozvíte, jak připojit virtuální počítač s Windows serverem k spravované doméně Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 4753cc9a98cd59c0c5d446b3d92280aabfb72c12
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474695"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Kurz: připojení virtuálního počítače s Windows serverem ke spravované doméně

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP, ověřování Kerberos/NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory. Se spravovanými doménami Azure služba AD DS můžete v Azure poskytovat funkce pro připojení k doméně a správu virtuálních počítačů (VM). V tomto kurzu se dozvíte, jak vytvořit virtuální počítač s Windows serverem a pak ho připojit k spravované doméně Azure služba AD DS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Windows serverem
> * Připojení k virtuálnímu počítači s Windows serverem k virtuální síti Azure
> * Připojte virtuální počítač k spravované doméně Azure služba AD DS.

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující zdroje:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby [vytvořte a nakonfigurujte instanci Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.
    * Ujistěte se, že jste provedli synchronizaci hodnot hash hesel Azure AD Connect nebo Samoobslužné resetování hesla, aby se účet mohl přihlásit ke spravované doméně Azure služba AD DS.

Pokud už máte virtuální počítač, ke kterému se chcete připojit k doméně, přejděte k části [připojení virtuálního počítače k spravované doméně Azure služba AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte virtuální počítač s Windows serverem, který se připojí ke spravované doméně Azure služba AD DS pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Vytvoření virtuálního počítače s Windows Serverem

Pokud chcete zjistit, jak připojit počítač k spravované doméně Azure služba AD DS, vytvoříme virtuální počítač s Windows serverem. Tento virtuální počítač je připojený ke službě Azure Virtual Network, která poskytuje připojení ke spravované doméně Azure služba AD DS. Proces připojení ke spravované doméně Azure služba AD DS je stejný jako při připojení k běžné místní doméně Active Directory Domain Services.

Pokud už máte virtuální počítač, ke kterému se chcete připojit k doméně, přejděte k části [připojení virtuálního počítače k spravované doméně Azure služba AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.

1. V **možnosti Začínáme vyberte** **Windows Server 2016 Datacenter**.

    ![Vyberte, pokud chcete vytvořit virtuální počítač s Windows serverem 2016 Datacenter v Azure Portal](./media/join-windows-vm/select-vm-image.png)

1. V okně **základy** nakonfigurujte základní nastavení virtuálního počítače. Pro *Možnosti dostupnosti*, *Obrázek*a *Velikost*ponechte výchozí nastavení.

    | Parametr            | Navrhovaná hodnota   |
    |----------------------|-------------------|
    | Skupina prostředků       | Vyberte nebo vytvořte skupinu prostředků, například *myResourceGroup* . |
    | Název virtuálního počítače | Zadejte název virtuálního počítače, třeba *myVM* . |
    | Oblast               | Vyberte oblast, ve které se má virtuální počítač vytvořit, například *východní USA* |
    | Uživatelské jméno             | Zadejte uživatelské jméno pro účet místního správce, který se má vytvořit na virtuálním počítači, například *azureuser* . |
    | Heslo             | Zadejte a potvrďte zabezpečené heslo pro místního správce, které se má na virtuálním počítači vytvořit. Nezadávejte přihlašovací údaje účtu uživatele domény. |

1. Virtuální počítače vytvořené v Azure ve výchozím nastavení nejsou přístupné z Internetu. Tato konfigurace pomáhá zlepšit zabezpečení virtuálního počítače a snižuje oblast pro možný útok. V dalším kroku tohoto kurzu se budete muset připojit k virtuálnímu počítači pomocí protokolu RDP (Remote Desktop Protocol) a pak připojit Windows Server k spravované doméně Azure služba AD DS.

    Když je protokol RDP povolený, pravděpodobně dojde k útokům prostřednictvím automatického přihlašování, které by mohlo v důsledku několika neúspěšných pokusů o přihlášení způsobit, že účty s běžnými názvy, jako je *správce* nebo *správce* . Protokol RDP by měl být povolen pouze v případě potřeby a omezen na sadu autorizovaných rozsahů IP adres. [Přístup k virtuálnímu počítači podle potřeby v Azure][jit-access] jako součást Azure Security Center můžou tyto krátkodobé a omezené relace RDP povolit. Můžete také [vytvořit a použít hostitele Azure bastionu (v současnosti ve verzi Preview)][azure-bastion] , abyste povolili přístup jenom prostřednictvím Azure Portal přes SSL.

    Pro tento kurz ručně povolte připojení RDP k virtuálnímu počítači.

    V části **veřejné příchozí porty**vyberte možnost pro **Povolení vybraných portů**. V rozevírací nabídce **Vyberte pro možnost vybrat příchozí porty**možnost *RDP (3389)* .

1. Až budete hotovi, vyberte **Další: disky**.
1. V rozevírací nabídce pro **typ disku s operačním systémem**zvolte *SSD úrovně Standard*a potom vyberte **Další: sítě**.
1. Váš virtuální počítač se musí připojit k podsíti virtuální sítě Azure, která může komunikovat s podsítí, do které se vaše spravovaná doména Azure služba AD DS nasadí. Doporučujeme, aby se spravovaná doména Azure služba AD DS nasadila do své vlastní vyhrazené podsítě. Nesaďte virtuální počítač ve stejné podsíti jako vaše spravovaná doména Azure služba AD DS.

    Existují dva hlavní způsoby, jak nasadit virtuální počítač a připojit se k příslušné podsíti virtuální sítě:
    
    * Vytvořte nebo vyberte existující podsíť ve stejné virtuální síti, jako je nasazená vaše spravovaná doména Azure služba AD DS.
    * Vyberte podsíť ve virtuální síti Azure, která je k ní připojená pomocí [partnerského vztahu virtuálních sítí Azure][vnet-peering].
    
    Pokud vyberete podsíť virtuální sítě, která není připojená k podsíti pro vaši instanci Azure služba AD DS, nemůžete připojit virtuální počítač ke spravované doméně. V tomto kurzu vytvoříme novou podsíť ve virtuální síti Azure.

    V podokně **sítě** vyberte virtuální síť, ve které je nasazená vaše doména spravovaná službou Azure služba AD DS, například *aaads-VNet* .
1. V tomto příkladu se zobrazuje existující *aaads-Subnet* , ke které je připojená doména Azure služba AD DS spravované. Nepřipojujte virtuální počítač k této podsíti. Pokud chcete vytvořit podsíť pro virtuální počítač, vyberte **spravovat konfiguraci podsítě**.

    ![Vyberte, chcete-li spravovat konfiguraci podsítě v Azure Portal](./media/join-windows-vm/manage-subnet.png)

1. V nabídce na levé straně okna virtuální síť vyberte **adresní prostor**. Virtuální síť se vytvoří s jedním adresním prostorem *10.0.1.0/24*, který se používá ve výchozí podsíti.

    Přidejte do virtuální sítě další rozsah IP adres. Velikost tohoto rozsahu adres a skutečný rozsah IP adres, které se mají použít, závisí na ostatních síťových prostředcích, které jsou už nasazené. Rozsah IP adres se nesmí překrývat s žádnými existujícími rozsahy adres ve vašem Azure nebo místním prostředí. Ujistěte se, že velikost rozsahu IP adres je dostatečně velká pro počet virtuálních počítačů, které chcete v podsíti nasadit.

    V následujícím příkladu se přidá další rozsah IP adres *10.0.2.0/24* . Až budete připraveni, vyberte **Uložit**.

    ![Přidejte do Azure Portal další rozsah IP adres virtuální sítě.](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. V nabídce vlevo v okně virtuální síť vyberte **podsítě**a pak vyberte **+ podsíť** a přidejte podsíť.

1. Vyberte **+ podsíť**a potom zadejte název podsítě, jako je například *Správa*. Zadejte **Rozsah adres (blok CIDR)** , například *10.0.2.0/24*. Ujistěte se, že se tento rozsah IP adres nepřekrývá s žádnými jinými existujícími rozsahy Azure nebo místními adresami. U ostatních možností ponechte výchozí hodnoty a pak vyberte **OK**.

    ![Vytvořte konfiguraci podsítě v Azure Portal](./media/join-windows-vm/create-subnet.png)

1. Vytvoření podsítě trvá několik sekund. Po vytvoření vyberte *X* , čímž zavřete okno podsíť.
1. Zpátky v podokně **sítě** Chcete-li vytvořit virtuální počítač, vyberte podsíť, kterou jste vytvořili, z rozevírací nabídky, jako je například *Správa*. Znovu se ujistěte, že jste vybrali správnou podsíť a nesadíte virtuální počítač ve stejné podsíti jako vaše spravovaná doména Azure služba AD DS.
1. U ostatních možností ponechte výchozí hodnoty a pak vyberte **Správa**.
1. Nastavte **diagnostiku spouštění** na *vypnuto*. U ostatních možností ponechte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
1. Zkontrolujte nastavení virtuálního počítače a pak vyberte **vytvořit**.

Vytvoření virtuálního počítače trvá několik minut. Azure Portal zobrazuje stav nasazení. Jakmile je virtuální počítač připravený, vyberte **Přejít k prostředku**.

![Po úspěšném vytvoření v Azure Portal přejít na prostředek virtuálního počítače](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Připojení k virtuálnímu počítači s Windows serverem

Teď se přihlaste k nově vytvořenému virtuálnímu počítači s Windows serverem pomocí protokolu RDP a připojíte se ke spravované doméně Azure služba AD DS. Použijte přihlašovací údaje místního správce, které jste zadali při vytváření virtuálního počítače v předchozím kroku, a to bez jakýchkoli stávajících přihlašovacích údajů do domény.

1. V podokně **Přehled** vyberte **připojit**.

    ![Připojení k virtuálnímu počítači s Windows v Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Vyberte možnost *Stažení souboru RDP*. Uložte tento soubor RDP ve webovém prohlížeči.
1. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, vyberte **Připojit**.
1. Zadejte přihlašovací údaje místního správce, které jste zadali v předchozím kroku, abyste mohli vytvořit virtuální počítač, například *localhost\azureuser* .
1. Pokud se během procesu přihlašování zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat** a připojte se.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Připojte virtuální počítač k spravované doméně Azure služba AD DS.

Po vytvoření virtuálního počítače a připojení RDP se teď připojte k virtuálnímu počítači s Windows serverem k spravované doméně Azure služba AD DS. Tento proces je stejný jako počítač připojující se k běžné místní Active Directory Domain Services doméně.

1. Pokud se při přihlášení k virtuálnímu počítači ve výchozím nastavení **Správce serveru** neotevře, vyberte nabídku **Start** a pak zvolte **Správce serveru**.
1. V levém podokně okna **Správce serveru** vyberte **místní server**. V části **vlastnosti** v pravém podokně vyberte **pracovní skupina**.

    ![Otevřete Správce serveru na virtuálním počítači a upravte vlastnost pracovní skupina.](./media/join-windows-vm/server-manager.png)

1. V okně **Vlastnosti systému** vyberte **změnit** a připojte se ke spravované doméně Azure služba AD DS.

    ![Výběr změny vlastností pracovní skupiny nebo domény](./media/join-windows-vm/change-domain.png)

1. Do pole **doména** zadejte název spravované domény Azure služba AD DS, například *contoso.com*, a pak vyberte **OK**.

    ![Zadejte spravovanou doménu Azure služba AD DS, ke které se chcete připojit.](./media/join-windows-vm/join-domain.png)

1. Zadejte přihlašovací údaje domény pro připojení k doméně. Použijte přihlašovací údaje uživatele, který patří do skupiny *Azure AD DC Administrators* . Pouze členové této skupiny mají oprávnění k připojení počítačů k spravované doméně Azure služba AD DS. Účet musí být součástí spravované domény Azure služba AD DS nebo tenanta Azure AD. účty z externích adresářů přidružených k vašemu tenantovi Azure AD se během procesu připojení k doméně nemůžou správně ověřit. Pověření účtu lze zadat jedním z následujících způsobů:

    * **Formát UPN** (doporučeno) – zadejte příponu hlavního názvu uživatele (UPN) pro uživatelský účet, jak je nakonfigurováno ve službě Azure AD. Například přípona UPN uživatele *contosoadmin* by byla `contosoadmin@contoso.onmicrosoft.com`. K dispozici je několik běžných případů použití, kdy je možné použít spolehlivý formát UPN pro přihlášení k doméně, nikoli formát *sAMAccountName* :
        * Pokud je předpona hlavního názvu uživatele (UPN) dlouhá, například *deehasareallylongname*, může být parametr *sAMAccountName* automaticky vygenerován.
        * Pokud má v tenantovi Azure AD stejnou předponu hlavního názvu uživatele (UPN), jako je například *Dee*, může se automaticky vygenerovat jejich formát *sAMAccountName* .
    * **Formát sAMAccountName** – zadejte název účtu ve formátu *sAMAccountName* . Například *sAMAccountName* uživatele *contosoadmin* by bylo `CONTOSO\contosoadmin`.

1. Připojení k spravované doméně Azure služba AD DS může trvat několik sekund. Po dokončení se vám zobrazí následující zpráva, která vás vítá:

    ![Vítejte v doméně](./media/join-windows-vm/join-domain-successful.png)

    Pokračujte výběrem tlačítka **OK**.

1. Pokud chcete dokončit proces připojení ke spravované doméně Azure služba AD DS, restartujte virtuální počítač.

> [!TIP]
> Pomocí rutiny [Add-Computer][add-computer] můžete virtuální počítač připojit k doméně pomocí PowerShellu. V následujícím příkladu se připojí doména *Contoso* a virtuální počítač se restartuje. Po zobrazení výzvy zadejte přihlašovací údaje uživatele, který patří do skupiny *správců řadičů domény Azure AD* :
>
> `Add-Computer -DomainName CONTOSO -Restart`
>
> K připojení virtuálního počítače k doméně bez připojení k virtuálnímu počítači a ruční konfiguraci připojení můžete použít rutinu [set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell.

Po restartování virtuálního počítače s Windows serverem se do virtuálního počítače odešlou všechny zásady, které se používají ve spravované doméně Azure služba AD DS. Nyní se můžete přihlásit k virtuálnímu počítači s Windows serverem pomocí příslušných přihlašovacích údajů domény.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V dalším kurzu použijete tento virtuální počítač s Windows serverem k instalaci nástrojů pro správu, které vám umožní spravovat spravovanou doménu Azure služba AD DS. Pokud v této sérii kurzů nechcete pokračovat, přečtěte si následující postup vyčištění a [zakažte RDP](#disable-rdp) nebo [Odstraňte virtuální počítač](#delete-the-vm). V opačném případě [přejděte k dalšímu kurzu](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Zrušení připojení virtuálního počítače ze spravované domény Azure služba AD DS

Pokud chcete virtuální počítač odebrat ze spravované domény Azure služba AD DS, postupujte podle kroků znovu a [Připojte se k virtuálnímu počítači k doméně](#join-the-vm-to-the-azure-ad-ds-managed-domain). Místo připojení ke spravované doméně Azure služba AD DS se můžete rozhodnout připojit k pracovní skupině, jako je například výchozí *pracovní skupina*. Po restartování virtuálního počítače se objekt počítače odebere ze spravované domény Azure služba AD DS.

Pokud [virtuální počítač odstraníte](#delete-the-vm) bez odpojení od domény, bude v Azure služba AD DS ponechán objekt osamocený počítač.

### <a name="disable-rdp"></a>Zakázat protokol RDP

Pokud budete nadále používat virtuální počítač s Windows serverem, který jste vytvořili v tomto kurzu, abyste mohli spouštět vlastní aplikace nebo úlohy, odvoláte, že se protokol RDP otevřel přes Internet. Aby bylo možné zvýšit zabezpečení a snížit riziko útoku, měl by být protokol RDP zakázán po internetu. Pokud chcete zakázat protokol RDP na virtuální počítač s Windows serverem přes Internet, proveďte následující kroky:

1. V nabídce na levé straně vyberte **skupiny prostředků** .
1. Vyberte skupinu prostředků, například *myResourceGroup*.
1. Zvolte svůj virtuální počítač, například *myVM*, a pak vyberte *sítě*.
1. V části **pravidla zabezpečení příchozí sítě** pro skupinu zabezpečení sítě vyberte pravidlo, které povoluje protokol RDP, a pak zvolte **Odstranit**. Odebrání příchozího pravidla zabezpečení trvá několik sekund.

### <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Pokud tento virtuální počítač s Windows serverem nepoužíváte, odstraňte virtuální počítač pomocí následujících kroků:

1. V nabídce na levé straně vyberte **skupiny prostředků** .
1. Vyberte skupinu prostředků, například *myResourceGroup*.
1. Zvolte svůj virtuální počítač, například *myVM*, a pak vyberte **Odstranit**. Výběrem **Ano** potvrďte odstranění prostředku. Odstranění virtuálního počítače trvá několik minut.
1. Po odstranění virtuálního počítače vyberte disk s operačním systémem, síťovou kartu a všechny další prostředky s předponou *myVM-* prefix a odstraňte je.

## <a name="troubleshoot-domain-join-issues"></a>Řešení potíží s připojením k doméně

Virtuální počítač s Windows serverem by se měl úspěšně připojit ke spravované doméně Azure služba AD DS. stejným způsobem jako běžný místní počítač by se měl připojit k doméně Active Directory Domain Services. Pokud se virtuální počítač s Windows serverem nemůže připojit ke spravované doméně Azure služba AD DS, znamená to, že se jedná o připojení nebo problém související s přihlašovacími údaji. Pokud se chcete úspěšně připojit ke spravované doméně, přečtěte si následující části věnované odstraňování potíží.

### <a name="connectivity-issues"></a>Potíže s připojením

Pokud neobdržíte výzvu s výzvou k připojení k doméně, dojde k problému s připojením. Virtuální počítač se nemůže připojit ke spravované doméně Azure služba AD DS ve virtuální síti.

Po vyzkoušení každého z těchto kroků pro řešení potíží se znovu pokuste připojit virtuální počítač s Windows serverem ke spravované doméně.

* Ověřte, že je virtuální počítač připojený ke stejné virtuální síti, na které je Azure služba AD DS povolený, nebo má připojení k síti s partnerským vztahem.
* Zkuste provést příkaz k otestování názvu domény DNS spravované domény, například `ping contoso.com`.
    * Pokud požadavek na příkaz k odeslání neproběhne úspěšně, pokuste se provést příkaz k otestování IP adres pro spravovanou doménu, například `ping 10.0.0.4`. IP adresa vašeho prostředí se zobrazí na stránce *vlastnosti* , když v seznamu prostředků Azure vyberete spravovanou doménu Azure služba AD DS.
    * Pokud můžete provést příkaz k otestování IP adresy, ale ne k doméně, služba DNS je možná nesprávně nakonfigurovaná. Potvrďte, že IP adresy spravované domény jsou nakonfigurované jako servery DNS pro virtuální síť.
* Zkuste vyprázdnit mezipaměť překladače DNS na virtuálním počítači pomocí příkazu `ipconfig /flushdns`.

### <a name="credentials-related-issues"></a>Problémy související s přihlašovacími údaji

Pokud se zobrazí výzva, která žádá o přihlašovací údaje pro připojení k doméně, ale po zadání těchto přihlašovacích údajů se virtuální počítač může připojit k spravované doméně Azure služba AD DS. Přihlašovací údaje, které jste zadali, a pak umožněte virtuálnímu počítači připojit se ke spravované doméně Azure služba AD DS.

Po vyzkoušení každého z těchto kroků pro řešení potíží se znovu pokuste připojit virtuální počítač s Windows serverem ke spravované doméně.

* Ujistěte se, že uživatelský účet, který zadáte, patří do skupiny *Správci AAD DC* .
* Potvrďte, že je účet součástí spravované domény Azure služba AD DS nebo tenanta Azure AD. Účty z externích adresářů přidružených k vašemu tenantovi Azure AD se během procesu připojení k doméně nedají správně ověřit.
* Zkuste zadat přihlašovací údaje, jako je například `contosoadmin@contoso.onmicrosoft.com`, pomocí formátu UPN. Pokud ve vašem tenantovi máte mnoho uživatelů se stejnou předponou UPN nebo pokud je předpona hlavního názvu uživatele nadlimitní, je možné, že se účet *sAMAccountName* pro váš účet vygeneruje automaticky. V těchto případech se formát *sAMAccountName* pro váš účet může lišit od toho, co očekáváte nebo používáte ve vaší místní doméně.
* Ověřte, že jste [povolili synchronizaci hesel][password-sync] s vaší spravovanou doménou. Bez tohoto kroku konfigurace nebudou k dispozici požadované hodnoty hash hesla ve spravované doméně Azure služba AD DS ke správnému ověření vašeho pokusu o přihlášení.
* Počkejte, než se synchronizace hesel dokončí. Při změně hesla uživatelského účtu se při automatické synchronizaci na pozadí z Azure AD aktualizuje heslo v Azure služba AD DS. Aby bylo heslo k dispozici pro použití v rámci připojení k doméně, bude chvíli trvat.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Windows serverem
> * Připojení k virtuálnímu počítači s Windows serverem k virtuální síti Azure
> * Připojte virtuální počítač k spravované doméně Azure služba AD DS.

Pokud chcete spravovat Azure služba AD DS spravované domény, nakonfigurujte virtuální počítač pro správu pomocí Centrum správy služby Active Directory (ADAC).

> [!div class="nextstepaction"]
> [Instalace nástrojů pro správu na virtuálním počítači pro správu](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[jit-access]: ../security-center/security-center-just-in-time.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
