---
title: Připojení virtuálního počítače s Windows serverem k spravované doméně Azure AD Domain Services | Microsoft Docs
description: V tomto kurzu se dozvíte, jak připojit virtuální počítač s Windows serverem k spravované doméně Azure Active Directory Domain Services.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 869c827485d9b7a6baf68d2619af98d4c2ee82b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619568"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Kurz: připojení virtuálního počítače s Windows serverem k spravované doméně Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP, ověřování Kerberos/NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory. Se spravovanými doménami Azure služba AD DS můžete v Azure poskytovat funkce pro připojení k doméně a správu virtuálních počítačů (VM). V tomto kurzu se dozvíte, jak vytvořit virtuální počítač s Windows serverem a pak ho připojit ke spravované doméně.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Windows serverem
> * Připojení virtuálního počítače s Windows serverem k virtuální síti Azure
> * Připojte virtuální počítač k spravované doméně.

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující zdroje:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény.
    * Ujistěte se, že jste provedli synchronizaci hodnot hash hesel Azure AD Connect a Samoobslužné resetování hesla, aby se účet mohl přihlásit ke spravované doméně.
* Hostitel Azure bastionu nasazený ve vaší virtuální síti Azure služba AD DS.
    * V případě potřeby [Vytvořte hostitele Azure bastionu][azure-bastion].

Pokud už máte virtuální počítač, ke kterému se chcete připojit k doméně, přejděte k části [připojení virtuálního počítače ke spravované doméně](#join-the-vm-to-the-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte virtuální počítač s Windows serverem pro připojení ke spravované doméně pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Vytvoření virtuálního počítače s Windows serverem

Pokud chcete zjistit, jak připojit počítač ke spravované doméně, vytvoříme virtuální počítač s Windows serverem. Tento virtuální počítač je připojený ke službě Azure Virtual Network, která poskytuje připojení ke spravované doméně. Proces připojení ke spravované doméně je stejný jako při připojení k běžné místní Active Directory Domain Services doméně.

Pokud už máte virtuální počítač, ke kterému se chcete připojit k doméně, přejděte k části [připojení virtuálního počítače ke spravované doméně](#join-the-vm-to-the-managed-domain).

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.

1. V **možnosti Začínáme vyberte** **Windows Server 2016 Datacenter**.

    ![Vyberte, pokud chcete vytvořit virtuální počítač s Windows serverem 2016 Datacenter v Azure Portal](./media/join-windows-vm/select-vm-image.png)

1. V okně **základy** nakonfigurujte základní nastavení virtuálního počítače. Pro *Možnosti dostupnosti*, *Obrázek* a *Velikost* ponechte výchozí nastavení.

    | Parametr            | Navrhovaná hodnota   |
    |----------------------|-------------------|
    | Skupina prostředků       | Vyberte nebo vytvořte skupinu prostředků, například *myResourceGroup* . |
    | Název virtuálního počítače | Zadejte název virtuálního počítače, třeba *myVM* . |
    | Oblast               | Vyberte oblast, ve které se má virtuální počítač vytvořit, například *východní USA* |
    | Uživatelské jméno             | Zadejte uživatelské jméno pro účet místního správce, který se má vytvořit na virtuálním počítači, například *azureuser* . |
    | Heslo             | Zadejte a potvrďte zabezpečené heslo pro místního správce, které se má na virtuálním počítači vytvořit. Nezadávejte přihlašovací údaje účtu uživatele domény. |

1. Virtuální počítače vytvořené v Azure jsou ve výchozím nastavení přístupné z Internetu pomocí protokolu RDP. Když je protokol RDP povolený, pravděpodobně dojde k automatickým útokům při přihlašování, které by mohlo vést k neúspěšným pokusům o přihlášení, které by mohly začínat pomocí běžných názvů, jako je *správce* nebo *správce* .

    Protokol RDP by měl být povolen pouze v případě potřeby a omezen na sadu autorizovaných rozsahů IP adres. Tato konfigurace pomáhá zlepšit zabezpečení virtuálního počítače a snižuje oblast pro možný útok. Nebo můžete vytvořit a použít hostitele Azure bastionu, který umožňuje přístup pouze prostřednictvím Azure Portal přes protokol TLS. V dalším kroku tohoto kurzu použijete hostitele Azure bastionu k zabezpečenému připojení k virtuálnímu počítači.

    V části **veřejné příchozí porty** vyberte *žádné*.

1. Až budete hotovi, vyberte **Další: disky**.
1. V rozevírací nabídce pro **typ disku s operačním systémem** zvolte *SSD úrovně Standard* a potom vyberte **Další: sítě**.
1. Váš virtuální počítač se musí připojit k podsíti virtuální sítě Azure, která může komunikovat s podsítí, do které jste spravovanou doménu nasadili. Doporučujeme, aby se spravovaná doména nasadila do své vlastní vyhrazené podsítě. Nesaďte virtuální počítač ve stejné podsíti jako vaše spravovaná doména.

    Existují dva hlavní způsoby, jak nasadit virtuální počítač a připojit se k příslušné podsíti virtuální sítě:
    
    * Vytvořte nebo vyberte existující podsíť ve stejné virtuální síti, ve které je nasazená vaše spravovaná doména.
    * Vyberte podsíť ve virtuální síti Azure, která je k ní připojená pomocí [partnerského vztahu virtuálních sítí Azure][vnet-peering].
    
    Pokud vyberete podsíť virtuální sítě, která není připojená k podsíti vaší spravované domény, nemůžete připojit virtuální počítač ke spravované doméně. V tomto kurzu vytvoříme novou podsíť ve virtuální síti Azure.

    V podokně **sítě** vyberte virtuální síť, ve které se spravovaná doména nasadí, třeba *aaads-VNet* .
1. V tomto příkladu se zobrazí existující *aaads-Subnet* , ke které je připojená spravovaná doména. Nepřipojujte virtuální počítač k této podsíti. Pokud chcete vytvořit podsíť pro virtuální počítač, vyberte **spravovat konfiguraci podsítě**.

    ![Vyberte, chcete-li spravovat konfiguraci podsítě v Azure Portal](./media/join-windows-vm/manage-subnet.png)

1. V nabídce na levé straně okna virtuální síť vyberte **adresní prostor**. Virtuální síť se vytvoří s jedním adresním prostorem *10.0.2.0/24*, který se používá ve výchozí podsíti. Další podsítě, například pro *úlohy* nebo Azure bastionu, mohou již existovat také.

    Přidejte do virtuální sítě další rozsah IP adres. Velikost tohoto rozsahu adres a skutečný rozsah IP adres, které se mají použít, závisí na ostatních síťových prostředcích, které jsou už nasazené. Rozsah IP adres se nesmí překrývat s žádnými existujícími rozsahy adres ve vašem Azure nebo místním prostředí. Ujistěte se, že velikost rozsahu IP adres je dostatečně velká pro počet virtuálních počítačů, které chcete v podsíti nasadit.

    V následujícím příkladu se přidá další rozsah IP adres *10.0.5.0/24* . Až budete připraveni, vyberte **Uložit**.

    ![Přidejte do Azure Portal další rozsah IP adres virtuální sítě.](./media/join-windows-vm/add-vnet-address-range.png)

1. V nabídce vlevo v okně virtuální síť vyberte **podsítě** a pak vyberte **+ podsíť** a přidejte podsíť.

1. Vyberte **+ podsíť** a potom zadejte název podsítě, jako je například *Správa*. Zadejte **Rozsah adres (blok CIDR)**, například *10.0.5.0/24*. Ujistěte se, že se tento rozsah IP adres nepřekrývá s žádnými jinými existujícími rozsahy Azure nebo místními adresami. U ostatních možností ponechte výchozí hodnoty a pak vyberte **OK**.

    ![Vytvořte konfiguraci podsítě v Azure Portal](./media/join-windows-vm/create-subnet.png)

1. Vytvoření podsítě trvá několik sekund. Po vytvoření vyberte *X* , čímž zavřete okno podsíť.
1. Zpátky v podokně **sítě** Chcete-li vytvořit virtuální počítač, vyberte podsíť, kterou jste vytvořili, z rozevírací nabídky, jako je například *Správa*. Znovu se ujistěte, že jste vybrali správnou podsíť a nesadíte virtuální počítač ve stejné podsíti jako vaše spravovaná doména.
1. V rozevírací nabídce pro **veřejnou IP adresu** vyberte *None (žádné* ). Když v tomto kurzu použijete Azure bastionu a připojíte se ke správě, nepotřebujete k virtuálnímu počítači přiřazenou veřejnou IP adresu.
1. U ostatních možností ponechte výchozí hodnoty a pak vyberte **Správa**.
1. Nastavte **diagnostiku spouštění** na *vypnuto*. U ostatních možností ponechte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
1. Zkontrolujte nastavení virtuálního počítače a pak vyberte **vytvořit**.

Vytvoření virtuálního počítače trvá několik minut. Azure Portal zobrazuje stav nasazení. Jakmile je virtuální počítač připravený, vyberte **Přejít k prostředku**.

![Po úspěšném vytvoření v Azure Portal přejít na prostředek virtuálního počítače](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Připojení k virtuálnímu počítači s Windows serverem

K zabezpečenému připojení k virtuálním počítačům použijte hostitele Azure bastionu. S Azure bastionu je spravovaný hostitel nasazený do vaší virtuální sítě a poskytuje webové připojení RDP nebo SSH k virtuálním počítačům. Pro virtuální počítače nejsou nutné žádné veřejné IP adresy a nemusíte otevírat pravidla skupiny zabezpečení sítě pro externí vzdálený provoz. K virtuálním počítačům se můžete připojit pomocí Azure Portal z webového prohlížeče. V případě potřeby [Vytvořte hostitele Azure bastionu][azure-bastion].

Pokud se chcete k VIRTUÁLNÍmu počítači připojit pomocí hostitele bastionu, proveďte následující kroky:

1. V podokně **Přehled** pro váš virtuální počítač vyberte **připojit** a pak **bastionu**.

    ![Připojení k virtuálnímu počítači s Windows pomocí bastionu v Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Zadejte přihlašovací údaje pro váš virtuální počítač, který jste zadali v předchozí části, a pak vyberte **připojit**.

   ![Připojení prostřednictvím hostitele bastionu v Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

V případě potřeby umožněte webovému prohlížeči otevřít automaticky otevíraná okna pro zobrazení připojení bastionu. Vytvoření připojení k VIRTUÁLNÍmu počítači trvá několik sekund.

## <a name="join-the-vm-to-the-managed-domain"></a>Připojte virtuální počítač k spravované doméně.

Když je vytvořený virtuální počítač a webové připojení RDP vytvořené pomocí Azure bastionu, teď se ke spravované doméně připojí virtuální počítač s Windows serverem. Tento proces je stejný jako počítač připojující se k běžné místní Active Directory Domain Services doméně.

1. Pokud se při přihlášení k virtuálnímu počítači ve výchozím nastavení **Správce serveru** neotevře, vyberte nabídku **Start** a pak zvolte **Správce serveru**.
1. V levém podokně okna **Správce serveru** vyberte **místní server**. V části **vlastnosti** v pravém podokně vyberte **pracovní skupina**.

    ![Otevřete Správce serveru na virtuálním počítači a upravte vlastnost pracovní skupina.](./media/join-windows-vm/server-manager.png)

1. V okně **Vlastnosti systému** vyberte **změnit** a připojte se ke spravované doméně.

    ![Výběr změny vlastností pracovní skupiny nebo domény](./media/join-windows-vm/change-domain.png)

1. Do pole **doména** zadejte název spravované domény, například *aaddscontoso.com*, a pak vyberte **OK**.

    ![Zadejte spravovanou doménu, ke které se chcete připojit.](./media/join-windows-vm/join-domain.png)

1. Zadejte přihlašovací údaje domény pro připojení k doméně. Zadejte přihlašovací údaje uživatele, který je součástí spravované domény. Účet musí být součástí spravované domény nebo tenanta Azure AD – účty z externích adresářů přidružených k vašemu tenantovi Azure AD se nemůžou správně ověřit během procesu připojení k doméně.

    Pověření účtu lze zadat jedním z následujících způsobů:

    * **Formát UPN** (doporučeno) – zadejte příponu hlavního názvu uživatele (UPN) pro uživatelský účet, jak je nakonfigurováno ve službě Azure AD. Například přípona hlavního názvu uživatele (UPN) uživatelského *contosoadmin* by byla `contosoadmin@aaddscontoso.onmicrosoft.com` . K dispozici je několik běžných případů použití, kdy je možné použít spolehlivý formát UPN pro přihlášení k doméně, nikoli formát *sAMAccountName* :
        * Pokud je předpona hlavního názvu uživatele (UPN) dlouhá, například *deehasareallylongname*, může být parametr *sAMAccountName* automaticky vygenerován.
        * Pokud má v tenantovi Azure AD stejnou předponu hlavního názvu uživatele (UPN), jako je například *Dee*, může se automaticky vygenerovat jejich formát *sAMAccountName* .
    * **Formát sAMAccountName** – zadejte název účtu ve formátu *sAMAccountName* . Například *sAMAccountName* pro uživatele *contosoadmin* by byl `AADDSCONTOSO\contosoadmin` .

1. Připojení ke spravované doméně trvá několik sekund. Po dokončení se vám zobrazí následující zpráva, která vás vítá:

    ![Vítejte v doméně](./media/join-windows-vm/join-domain-successful.png)

    Pokračujte výběrem tlačítka **OK**.

1. Pokud chcete dokončit proces připojení ke spravované doméně, restartujte virtuální počítač.

> [!TIP]
> Pomocí rutiny [Add-Computer][add-computer] můžete virtuální počítač připojit k doméně pomocí PowerShellu. V následujícím příkladu se připojí doména *AADDSCONTOSO* a pak se virtuální počítač restartuje. Po zobrazení výzvy zadejte přihlašovací údaje uživatele, který je součástí spravované domény:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> K připojení virtuálního počítače k doméně bez připojení k virtuálnímu počítači a ruční konfiguraci připojení můžete použít rutinu [set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell.

Po restartování virtuálního počítače s Windows serverem se do virtuálního počítače odešlou všechny zásady použité ve spravované doméně. Nyní se můžete přihlásit k virtuálnímu počítači s Windows serverem pomocí příslušných přihlašovacích údajů domény.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V dalším kurzu použijete tento virtuální počítač s Windows serverem k instalaci nástrojů pro správu, které vám umožní spravovat spravovanou doménu. Pokud v této sérii kurzů nechcete pokračovat, Projděte si následující postup vyčištění a [Odstraňte virtuální počítač](#delete-the-vm). V opačném případě [přejděte k dalšímu kurzu](#next-steps).

### <a name="unjoin-the-vm-from-the-managed-domain"></a>Odpojte virtuální počítač ze spravované domény.

Pokud chcete virtuální počítač odebrat ze spravované domény, postupujte podle kroků znovu a [Připojte se k virtuálnímu počítači k doméně](#join-the-vm-to-the-managed-domain). Místo připojení ke spravované doméně vyberte možnost připojit se k pracovní skupině, jako je například výchozí *pracovní skupina*. Po restartování virtuálního počítače se objekt počítače odebere ze spravované domény.

Pokud [virtuální počítač odstraníte](#delete-the-vm) bez odpojení od domény, bude v Azure služba AD DS ponechán objekt osamocený počítač.

### <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Pokud tento virtuální počítač s Windows serverem nepoužíváte, odstraňte virtuální počítač pomocí následujících kroků:

1. V nabídce na levé straně vyberte **skupiny prostředků** .
1. Vyberte skupinu prostředků, například *myResourceGroup*.
1. Zvolte svůj virtuální počítač, například *myVM*, a pak vyberte **Odstranit**. Výběrem **Ano** potvrďte odstranění prostředku. Odstranění virtuálního počítače trvá několik minut.
1. Po odstranění virtuálního počítače vyberte disk s operačním systémem, síťovou kartu a všechny další prostředky s předponou *myVM-* prefix a odstraňte je.

## <a name="troubleshoot-domain-join-issues"></a>Řešení potíží s připojením k doméně

Virtuální počítač s Windows serverem by se měl úspěšně připojit ke spravované doméně. stejným způsobem jako běžný místní počítač by se měl připojit k doméně Active Directory Domain Services. Pokud se virtuální počítač s Windows serverem nemůže připojit ke spravované doméně, znamená to, že se jedná o připojení nebo problém související s přihlašovacími údaji. Pokud se chcete úspěšně připojit ke spravované doméně, přečtěte si následující části věnované odstraňování potíží.

### <a name="connectivity-issues"></a>Problémy s připojením

Pokud neobdržíte výzvu s výzvou k připojení k doméně, dojde k problému s připojením. Virtuální počítač se nemůže připojit ke spravované doméně ve virtuální síti.

Po vyzkoušení každého z těchto kroků pro řešení potíží se znovu pokuste připojit virtuální počítač s Windows serverem ke spravované doméně.

* Ověřte, že je virtuální počítač připojený ke stejné virtuální síti, na které je Azure služba AD DS povolený, nebo má připojení k síti s partnerským vztahem.
* Zkuste provést příkaz k otestování názvu domény DNS spravované domény, třeba `ping aaddscontoso.com` .
    * Pokud se požadavek na příkaz k odeslání nepovede, zkuste provést příkaz k otestování IP adres pro spravovanou doménu, třeba `ping 10.0.0.4` . IP adresa vašeho prostředí se zobrazí na stránce *vlastnosti* při výběru spravované domény ze seznamu prostředků Azure.
    * Pokud můžete provést příkaz k otestování IP adresy, ale ne k doméně, služba DNS je možná nesprávně nakonfigurovaná. Potvrďte, že IP adresy spravované domény jsou nakonfigurované jako servery DNS pro virtuální síť.
* Zkuste vyprázdnit mezipaměť překladače DNS na virtuálním počítači pomocí `ipconfig /flushdns` příkazu.

### <a name="credentials-related-issues"></a>Problémy související s přihlašovacími údaji

Pokud se zobrazí výzva, která žádá o přihlašovací údaje pro připojení k doméně, ale po zadání těchto přihlašovacích údajů se virtuální počítač může připojit ke spravované doméně. Přihlašovací údaje, které jste zadali, neumožní virtuálnímu počítači připojit se ke spravované doméně.

Po vyzkoušení každého z těchto kroků pro řešení potíží se znovu pokuste připojit virtuální počítač s Windows serverem ke spravované doméně.

* Ujistěte se, že uživatelský účet, který zadáte, patří do spravované domény.
* Potvrďte, že je účet součástí spravované domény nebo tenanta Azure AD. Účty z externích adresářů přidružených k vašemu tenantovi Azure AD se během procesu připojení k doméně nedají správně ověřit.
* Zkuste zadat přihlašovací údaje pomocí formátu UPN, například `contosoadmin@aaddscontoso.onmicrosoft.com` . Pokud ve vašem tenantovi máte mnoho uživatelů se stejnou předponou UPN nebo pokud je předpona hlavního názvu uživatele nadlimitní, je možné, že se účet *sAMAccountName* pro váš účet vygeneruje automaticky. V těchto případech se formát *sAMAccountName* pro váš účet může lišit od toho, co očekáváte nebo používáte ve vaší místní doméně.
* Ověřte, že jste [povolili synchronizaci hesel][password-sync] s vaší spravovanou doménou. Bez tohoto kroku konfigurace nebudou ve spravované doméně k dispozici požadované hodnoty hash hesla pro správné ověření vašeho pokusu o přihlášení.
* Počkejte, než se synchronizace hesel dokončí. Při změně hesla uživatelského účtu se při automatické synchronizaci na pozadí z Azure AD aktualizuje heslo v Azure služba AD DS. Aby bylo heslo k dispozici pro použití v rámci připojení k doméně, bude chvíli trvat.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Windows serverem
> * Připojení k virtuálnímu počítači s Windows serverem k virtuální síti Azure
> * Připojte virtuální počítač k spravované doméně.

Pokud chcete spravovat spravovanou doménu, nakonfigurujte virtuální počítač pro správu pomocí Centrum správy služby Active Directory (ADAC).

> [!div class="nextstepaction"]
> [Instalace nástrojů pro správu na virtuálním počítači pro správu](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: ./tutorial-create-instance.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension