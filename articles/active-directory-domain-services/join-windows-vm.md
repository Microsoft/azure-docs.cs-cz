---
title: Připojení virtuálního virtuálního virtuálního vstupu systému Windows Server ke spravované doméně | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak připojit virtuální počítač se systémem Windows Server ke spravované doméně spravované službou Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 1ac508fc9fee07482e475c46e1db262c8bfa1a12
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476234"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Kurz: Připojení virtuálního počítače se systémem Windows Server ke spravované doméně

Služba Azure Active Directory Domain Services (Azure AD DS) poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, ověřování LDAP, Kerberos/NTLM, které je plně kompatibilní se službou Active Directory systému Windows Server. Se spravovanou doménou Azure AD DS můžete poskytovat funkce připojení k doméně a správu virtuálních počítačů (VM) v Azure. Tento kurz ukazuje, jak vytvořit virtuální počítač se systémem Windows Server a pak ho připojit ke spravované doméně Azure AD DS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního virtuálního ms pro Windows Server
> * Připojení virtuálního počítače Windows Server k virtuální síti Azure
> * Připojení virtuálního počítače ke spravované doméně Azure AD DS

Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující zdroje:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby [vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény Azure AD DS.
    * Ujistěte se, že azure ad připojení synchronizace hash nebo samoobslužné resetování hesla byla provedena tak, aby účet je možné přihlásit do spravované domény Azure AD DS.
* Hostitel Azure Bastion nasazený ve vaší virtuální síti Azure AD DS.
    * V případě potřeby [vytvořte hostitele Azure Bastion][azure-bastion].

Pokud už máte virtuální počítač, ke kterému se chcete připojit k doméně, přejděte do části a [připojte virtuální počítač ke spravované doméně Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte virtuální počítač se systémem Windows Server pro připojení ke spravované doméně Azure AD DS pomocí portálu Azure. Chcete-li začít, nejprve se přihlaste na [portál Azure](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Vytvoření virtuálního počítače se systémem Windows Server

Chcete-li zjistit, jak se připojit k počítači spravované domény Azure AD DS, pojďme vytvořit virtuální počítač se systémem Windows Server. Tento virtuální počítač je připojený k virtuální síti Azure, která poskytuje připojení ke spravované doméně Azure AD DS. Proces připojení ke spravované doméně Azure AD DS je stejný jako připojení k běžné místní doméně služby Active Directory Domain Services.

Pokud už máte virtuální počítač, ke kterému se chcete připojit k doméně, přejděte do části a [připojte virtuální počítač ke spravované doméně Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. V nabídce Portál Azure nebo na **domovské** stránce vyberte **Vytvořit prostředek**.

1. V **možnosti Začínáme**zvolte **Windows Server 2016 Datacenter**.

    ![Volba vytvoření virtuálního počítače datového centra Windows Server 2016 na webu Azure Portal](./media/join-windows-vm/select-vm-image.png)

1. V okně **Základy** nakonfigurujte základní nastavení virtuálního počítače. Ponechte výchozí hodnoty *pro možnosti dostupnosti*, *Obrázek*a *Velikost*.

    | Parametr            | Navrhovaná hodnota   |
    |----------------------|-------------------|
    | Skupina prostředků       | Výběr nebo vytvoření skupiny prostředků, například *myResourceGroup* |
    | Název virtuálního počítače | Zadejte název virtuálního_ *myVM* |
    | Region (Oblast)               | Vyberte oblast, ve které chcete vytvořit virtuální počítač, třeba *Východní USA.* |
    | Uživatelské jméno             | Zadejte uživatelské jméno pro účet místního správce, který chcete vytvořit na virtuálním počítači, jako je *azureuser.* |
    | Heslo             | Zadejte a potvrďte zabezpečené heslo, které má místní správce vytvořit na virtuálním počítači. Nezadávejte pověření uživatelského účtu domény. |

1. Ve výchozím nastavení virtuální počítače vytvořené v Azure jsou přístupné z internetu pomocí RDP. Je-li rdp povoleno, je pravděpodobné, že dojde k automatickému přihlášení, což může zakázat účty s běžnými názvy, jako je *například správce* nebo *správce* z důvodu několika neúspěšných po sobě jdoucích pokusů o přihlášení.

    Protokol RDP by měl být povolen pouze v případě potřeby a omezen na sadu autorizovaných rozsahů IP adres. Tato konfigurace pomáhá zlepšit zabezpečení virtuálního počítače a snižuje oblast pro potenciální útok. Nebo vytvořte a použijte hostitele Azure Bastion, který umožňuje přístup jenom prostřednictvím portálu Azure přes TLS. V dalším kroku tohoto kurzu použijete hostitele Azure Bastion k bezpečnému připojení k virtuálnímu počítači.

    V části **Veřejné příchozí porty**vyberte *Žádný*.

1. Až bude hotovo, vyberte **Další: Disky**.
1. V rozevírací nabídce pro **typ disku operačního systému**zvolte *Standardní SSD*a pak vyberte **Další: Síť**.
1. Váš virtuální počítač se musí připojit k podsíti virtuální sítě Azure, která může komunikovat s podsítí, do které se nasadí vaše spravovaná doména Azure AD DS. Doporučujeme, aby se spravovaná doména Azure AD DS nasazovala do vlastní vyhrazené podsítě. Nenasazujte virtuální počítač ve stejné podsíti jako spravovaná doména Azure AD DS.

    Existují dva hlavní způsoby nasazení virtuálního počítače a připojení k příslušné podsíti virtuální sítě:
    
    * Vytvořte nebo vyberte existující podsíť ve stejné virtuální síti jako vaše spravovaná doména Azure AD DS se nasadí.
    * Vyberte podsíť ve virtuální síti Azure, která je k ní připojená pomocí [partnerského vztahu virtuální sítě Azure][vnet-peering].
    
    Pokud vyberete podsíť virtuální sítě, která není připojená k podsíti pro vaši instanci Služby Azure AD DS, nemůžete připojit virtuální počítač ke spravované doméně. V tomto kurzu pojďme vytvořit novou podsíť ve virtuální síti Azure.

    V podokně **Sítě** vyberte virtuální síť, ve které se nasazuje vaše doména spravovaná službou Azure AD DS, například *aaads-vnet.*
1. V tomto příkladu se zobrazí existující *podsíť aaads,* ke které je připojena spravovaná doména Azure AD DS. Nepřipojujte virtuální počítač k této podsíti. Pokud chcete vytvořit podsíť pro virtuální počítače, vyberte **Spravovat konfiguraci podsítě**.

    ![Zvolte správu konfigurace podsítě na webu Azure Portal.](./media/join-windows-vm/manage-subnet.png)

1. V levé nabídce okna virtuální sítě vyberte **Možnost Adresní prostor**. Virtuální síť je vytvořena s jedním adresním prostorem *10.0.2.0/24*, který používá výchozí podsíť. Jiné podsítě, například pro *úlohy* nebo Azure Bastion může také již existují.

    Přidejte do virtuální sítě další rozsah IP adres. Velikost tohoto rozsahu adres a skutečný rozsah IP adres, který chcete použít, závisí na jiných síťových prostředcích, které jsou již nasazeny. Rozsah IP adres by se neměl překrývat s existujícími rozsahy adres ve vašem prostředí Azure nebo v místním prostředí. Ujistěte se, že velikost rozsahu IP adres dostatečně velký pro počet virtuálních počítačů, které očekáváte nasazení do podsítě.

    V následujícím příkladu je přidán další rozsah IP adres *10.0.5.0/24.* Až budete připraveni, vyberte **Uložit**.

    ![Přidání dalšího rozsahu IP adres virtuální sítě na webu Azure Portal](./media/join-windows-vm/add-vnet-address-range.png)

1. Dále v levé nabídce okna virtuální sítě vyberte **Podsítě**a pak zvolte **+ Podsíť** a přidejte podsíť.

1. Vyberte **+ Podsíť**a zadejte název podsítě, například *správu*. Zadejte **rozsah adres (blok CIDR),** například *10.0.5.0/24*. Ujistěte se, že tento rozsah IP adres se nepřekrývá s jinými existujícími rozsahy adres Azure nebo místními adresami. Ostatní možnosti ponechejte jako výchozí hodnoty a pak vyberte **OK**.

    ![Vytvoření konfigurace podsítě na webu Azure Portal](./media/join-windows-vm/create-subnet.png)

1. Vytvoření podsítě trvá několik sekund. Po vytvoření vyberte *X,* chcete-li okno podsítě zavřít.
1. V podokně **Sítě** vytvořte virtuální hod a vyberte podsíť, kterou jste vytvořili z rozevírací nabídky, například *správu*. Znovu se ujistěte, že jste zvolili správnou podsíť a nenasazujte virtuální počítač ve stejné podsíti jako spravovaná doména Azure AD DS.
1. V **části Veřejná IP adresa**vyberte v rozevírací nabídce možnost *Žádný,* protože pomocí Azure Bastion se připojíte ke správě a nepotřebujete přiřazenou veřejnou IP adresu.
1. Ostatní možnosti ponechejte jako výchozí hodnoty a pak vyberte **Správa**.
1. Nastavte **diagnostiku spuštění** na *vypnuto*. Ostatní možnosti ponechejte jako výchozí hodnoty a pak vyberte **Zkontrolovat + vytvořit**.
1. Zkontrolujte nastavení virtuálního počítače a vyberte **Vytvořit**.

Vytvoření virtuálního virtuálního mísy trvá několik minut. Portál Azure zobrazuje stav nasazení. Jakmile je virtuální virtuální ms připravený, vyberte **Přejít na prostředek**.

![Přejděte na prostředek virtuálního počítače na webu Azure Portal, jakmile se úspěšně vytvoří.](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Připojení k virtuálnímu virtuálnímu montovně se systémem Windows Server

Pokud se chcete bezpečně připojit k virtuálním počítačům, použijte hostitele Azure Bastion. Pomocí Azure Bastion se spravovaný hostitel nasadí do vaší virtuální sítě a poskytuje připojení RDP nebo SSH k virtuálním počítačům. Pro virtuální servery nejsou vyžadovány žádné veřejné IP adresy a není nutné otevírat pravidla skupiny zabezpečení sítě pro externí vzdálený provoz. K virtuálním počítačům se připojujete pomocí portálu Azure z webového prohlížeče.

Pokud chcete k virtuálnímu počítači použít hostitele bastionu, proveďte následující kroky:

1. V podokně **Přehled** pro virtuální počítač vyberte **Připojit**a pak **Bašta**.

    ![Připojení k virtuálnímu počítači s Windows pomocí bastionu na webu Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Zadejte přihlašovací údaje pro virtuální počítač, které jste zadali v předchozí části, a pak vyberte **Připojit**.

   ![Připojení prostřednictvím hostitele Bastion na webu Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

V případě potřeby povolte webovému prohlížeči otevřít automaticky otevíraná okna pro zobrazení připojení Bastion. Připojení k virtuálnímu počítači trvá několik sekund.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Připojení virtuálního počítače ke spravované doméně Azure AD DS

S vytvořeným virtuálním počítačem a webovým připojením RDP vytvořeným pomocí Azure Bastion teď připojíme virtuální počítač Windows Server ke spravované doméně Azure AD DS. Tento proces je stejný jako počítač, který se připojuje k běžné místní doméně služby Active Directory Domain Services.

1. Pokud **se Správce serveru** při přihlášení k virtuálnímu virtuálnímu virtuálnímu provozu ve výchozím nastavení neotevře, vyberte nabídku **Start** a pak zvolte Správce **serveru**.
1. V levém podokně okna **Správce serveru** vyberte možnost **Místní server**. V části **Vlastnosti** v pravém podokně zvolte **Pracovní skupina**.

    ![Otevření Správce serveru na virtuálním počítači a úprava vlastnosti pracovní skupiny](./media/join-windows-vm/server-manager.png)

1. V okně **Vlastnosti systému** vyberte **Změnit,** chcete-li se připojit ke spravované doméně Azure AD DS.

    ![Zvolte změnu vlastností pracovní skupiny nebo domény.](./media/join-windows-vm/change-domain.png)

1. Do pole **Doména** zadejte název spravované domény Služby Azure AD DS, například *aaddscontoso.com*, a pak vyberte **OK**.

    ![Určení spravované domény Azure AD DS, ke které se chcete připojit](./media/join-windows-vm/join-domain.png)

1. Zadejte pověření domény pro připojení k doméně. Použijte přihlašovací údaje pro uživatele, který je součástí spravované domény Azure AD DS. Účet musí být součástí spravované domény Azure AD DS nebo tenanta Azure AD – účty z externích adresářů přidružených k vašemu tenantovi Azure AD se během procesu připojení k doméně nemohou správně ověřit. Pověření účtu lze zadat jedním z následujících způsobů:

    * **Formát UPN** (doporučeno) – zadejte příponu hlavního uživatelského jména (UPN) pro uživatelský účet, jak je nakonfigurovaná ve službě Azure AD. Například přípona hlavního uživatele *contosoadmin* by `contosoadmin@aaddscontoso.onmicrosoft.com`. Existuje několik běžných případů použití, kdy formát UPN lze spolehlivě použít k přihlášení k doméně, nikoli k formátu *SAMAccountName:*
        * Pokud je dlouhá předpona hlavního názvu uživatele, například *deehasareallylongname*, může být automaticky generován *název SAMAccountName.*
        * Pokud má ve vašem tenantovi Azure AD stejnou předponu UPN více uživatelů, například *dee*, může být jejich formát *SAMAccountName* automaticky vygenerován.
    * **SamAccountName format** - Zadejte název účtu ve formátu *SAMAccountName.* Například *SAMAccountName* uživatele *contosoadmin* by `AADDSCONTOSO\contosoadmin`.

1. Připojení ke spravované doméně Azure AD DS trvá několik sekund. Po dokončení vás v doméně uvítá následující zpráva:

    ![Vítejte v doméně](./media/join-windows-vm/join-domain-successful.png)

    Pokračujte výběrem tlačítka **OK**.

1. Chcete-li dokončit proces připojení ke spravované doméně Azure AD DS, restartujte virtuální počítač.

> [!TIP]
> Můžete se připojit k virtuálnímu počítači pomocí prostředí PowerShell s [rutinou Add-Computer.][add-computer] Následující příklad připojí doménu *AADDSCONTOSO* a potom restartuje virtuální počítač. Po zobrazení výzvy zadejte přihlašovací údaje pro uživatele, který je součástí spravované domény Služby Azure AD DS:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Chcete-li připojit k virtuálnímu počítači bez připojení k němu a ručně konfigurovat připojení, můžete použít [Set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell rutina.

Po restartování virtuálního počítače se systémem Windows Server se všechny zásady použité ve spravované doméně Služby Azure AD DS zasouvají do virtuálního počítače. Nyní se také můžete přihlásit k virtuálnímu virtuálnímu ms systému Windows Server pomocí příslušných pověření domény.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V dalším kurzu použijete tento virtuální počítač se systémem Windows Server k instalaci nástrojů pro správu, které vám umožní spravovat spravovanou doménu Azure AD DS. Pokud nechcete pokračovat v této sérii kurzů, zkontrolujte následující kroky vyčištění k [odstranění virtuálního virtuálního uživatele](#delete-the-vm). V opačném případě [pokračujte dalším kurzem](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Zrušení připojení k virtuálnímu počítači ze spravované domény Azure AD DS

Pokud chcete virtuální počítač odebrat ze spravované domény Azure AD DS, postupujte podle pokynů znovu a [připojte virtuální počítač k doméně](#join-the-vm-to-the-azure-ad-ds-managed-domain). Místo připojení ke spravované doméně Azure AD DS zvolte připojení k pracovní skupině, jako je například výchozí *pracovní skupina*. Po restartování virtuálního počítače se objekt počítače odebere ze spravované domény Služby Azure AD DS.

Pokud [odstraníte virtuální počítač](#delete-the-vm) bez odpojení od domény, osamocený objekt počítače zůstane v Azure AD DS.

### <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Pokud tento virtuální virtuální hotel se systémem Windows Server nebudete používat, odstraňte virtuální hospovat pomocí následujících kroků:

1. V levé nabídce vyberte **Skupiny zdrojů.**
1. Vyberte skupinu prostředků, například *myResourceGroup*.
1. Zvolte virtuální počítač, jako *je myVM*, a pak vyberte **Odstranit**. Chcete-li odstranění prostředku potvrdit, vyberte **ano.** Odstranění virtuálního virtuálního mísy trvá několik minut.
1. Když se virtuální modul odstraní, vyberte disk operačního systému, kartu síťového rozhraní a další prostředky s předponou *myVM-* a odstraňte je.

## <a name="troubleshoot-domain-join-issues"></a>Poradce při potížích s připojením k doméně

Virtuální počítač se systémem Windows Server by se měl úspěšně připojit ke spravované doméně Azure AD DS, stejně jako běžný místní počítač by se připojil k doméně služby Active Directory Domain Services. Pokud se virtuální počítač se systémem Windows Server nemůže připojit ke spravované doméně Azure AD DS, znamená to, že došlo k problému s připojením nebo pověřeními. Projděte si následující části řešení potíží a úspěšně se připojte ke spravované doméně.

### <a name="connectivity-issues"></a>Problémy s připojením

Pokud neobdržíte výzvu, která vás požádá o pověření pro připojení k doméně, došlo k potížím s připojením. Virtuální počítač nemůže dosáhnout spravované domény Azure AD DS ve virtuální síti.

Po vyzkoušení každého z těchto kroků řešení potíží zkuste znovu připojit virtuální hod windows serveru ke spravované doméně.

* Ověřte, že virtuální počítač je připojený ke stejné virtuální síti, ve které je povolená služba Azure AD DS nebo má připojení k partnerské síti.
* Zkuste příkazping em název domény DNS spravované `ping aaddscontoso.com`domény, například .
    * Pokud se požadavek ping nezdaří, zkuste příkaz emiotovovat příkaz ping na adresy IP pro spravovanou doménu, například `ping 10.0.0.4`. IP adresa pro vaše prostředí se zobrazí na stránce *Vlastnosti,* když vyberete spravovanou doménu Azure AD DS ze seznamu prostředků Azure.
    * Pokud můžete příkazem ping příkazem ping na adresu IP, ale nikoli na doménu, může být služba DNS nesprávně nakonfigurována. Zkontrolujte, zda jsou adresy IP spravované domény nakonfigurovány jako servery DNS pro virtuální síť.
* Pokuste se vyprázdnit mezipaměť překladače DNS ve virtuálním počítači pomocí příkazu. `ipconfig /flushdns`

### <a name="credentials-related-issues"></a>Problémy související s pověřeními

Pokud se zobrazí výzva, která požádá o pověření pro připojení k doméně, ale po zadání těchto přihlašovacích údajů dojde k chybě, virtuální počítač se bude moct připojit ke spravované doméně Azure AD DS. Zadanými přihlašovacími údaji pak neumožňují virtuálnímu počítači připojit se ke spravované doméně Azure AD DS.

Po vyzkoušení každého z těchto kroků řešení potíží zkuste znovu připojit virtuální hod windows serveru ke spravované doméně.

* Ujistěte se, že zadaný uživatelský účet patří do spravované domény Azure AD DS.
* Zkontrolujte, že účet je součástí spravované domény Azure AD DS nebo klienta Azure AD. Účty z externích adresářů přidružených k vašemu tenantovi Azure AD se během procesu připojení k doméně nemohou správně ověřit.
* Zkuste pomocí formátu UPN určit přihlašovací `contosoadmin@aaddscontoso.onmicrosoft.com`údaje, například . Pokud je ve vašem tenantovi mnoho uživatelů se stejnou předponou HLAVNÍHO názvu uživatele nebo pokud je předpona hlavního názvu uživatele příliš dlouhá, může být název *SAMAccountName* pro váš účet automaticky vygenerován. V těchto případech se formát *SAMAccountName* pro váš účet může lišit od toho, co očekáváte nebo používáte v místní doméně.
* Zkontrolujte, zda jste [povolili synchronizaci hesel][password-sync] do spravované domény. Bez tohoto kroku konfigurace nebudou ve spravované doméně Služby Azure AD DS k dispozici požadované hashe hesel, aby bylo možné správně ověřit váš pokus o přihlášení.
* Počkejte na dokončení synchronizace hesel. Při změně hesla uživatelského účtu, automatická synchronizace na pozadí z Azure AD aktualizuje heslo v Azure AD DS. Trvá nějakou dobu, než heslo bude k dispozici pro použití připojení k doméně.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření virtuálního virtuálního ms pro Windows Server
> * Připojení k virtuálnímu počítači Windows Server k virtuální síti Azure
> * Připojení virtuálního počítače ke spravované doméně Azure AD DS

Chcete-li spravovat spravovanou doménu Azure AD DS, nakonfigurujte virtuální počítač pro správu pomocí Centra pro správu služby Active Directory (ADAC).

> [!div class="nextstepaction"]
> [Instalace nástrojů pro správu na virtuální počítač správy](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
