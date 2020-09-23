---
title: Přístup k virtuálnímu počítači za běhu v Azure Security Center | Microsoft Docs
description: Tento dokument ukazuje, jak přístup JIT (just-in-time VM) v Azure Security Center pomáhá řídit přístup k virtuálním počítačům Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 5b2446aa62b16dcf9773c367d87faac65d79fa0b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904856"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Zabezpečení portů pro správu pomocí přístupu za běhu

Pomocí funkce přístupu k virtuálnímu počítači (JIT) pro přístup k virtuálnímu počítači (VM) Azure Security Center zablokovat příchozí provoz do vaší služby Azure Virtual Machines. Tím se snižuje riziko útoků při poskytování snadného přístupu, když se potřebujete připojit k virtuálnímu počítači.

Úplné vysvětlení fungování JIT a základní logiky naleznete v tématu [vysvětlení za](just-in-time-explained.md)běhu.

Tato stránka vás učí, jak do programu zabezpečení zahrnout JIT. Dozvíte se, jak provést tyto akce: 

- **Povolit JIT na vašich virtuálních počítačích** – JIT můžete povolit pomocí vlastních možností pro jeden nebo víc virtuálních počítačů pomocí Security Center, PowerShellu nebo REST API. Alternativně můžete povolit JIT pomocí výchozích, pevně zakódovaných parametrů z virtuálních počítačů Azure. Pokud je povoleno, kompilátor JIT zamkne příchozí provoz do vašich virtuálních počítačů Azure vytvořením pravidla ve skupině zabezpečení sítě.
- **Požádat o přístup k virtuálnímu počítači, který má povolenou JIT** – cílem JIT je zajistit, že i když je váš příchozí provoz uzamčený, Security Center v případě potřeby i nadále poskytovat snadný přístup pro připojení k virtuálním počítačům. Můžete požádat o přístup k virtuálnímu počítači s podporou JIT z Security Center, virtuálních počítačů Azure, PowerShellu nebo REST API.
- **Audit aktivity** – aby se zajistilo správné zabezpečení virtuálních počítačů, zkontrolujte přístup k virtuálním počítačům s podporou JIT v rámci pravidelných kontrol zabezpečení.   



## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|Vyžaduje [Azure Defender pro servery](defender-for-servers-introduction.md) .|
|Podporované virtuální počítače:|![Ano ](./media/icons/yes-icon.png) virtuální počítače nasazené prostřednictvím Azure Resource Manager.<br>![Nejsou ](./media/icons/no-icon.png) nasazené žádné virtuální počítače s klasickými modely nasazení. [Přečtěte si další informace o těchto modelech nasazení](../azure-resource-manager/management/deployment-models.md).<br>![Žádné ](./media/icons/no-icon.png) virtuální počítače chráněné pomocí brány firewall Azure řízené nástrojem [Azure firewall Manager](https://docs.microsoft.com/azure/firewall-manager/overview)|
|Požadované role a oprávnění:|Role **Čtenář** a **SecurityReader** můžou zobrazovat stav a parametry JIT.<br>Chcete-li vytvořit vlastní role, které mohou pracovat s JIT, přečtěte si téma [jaká oprávnění jsou nutná ke konfiguraci a používání JIT?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit).<br>Chcete-li vytvořit roli s nejnižšími oprávněními pro uživatele, kteří potřebují vyžadovat přístup JIT k virtuálnímu počítači a provádět žádné jiné operace JIT, použijte [skript set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) ze stránky komunity GitHub Security Center.|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||


## <a name="enable-jit-vm-access"></a>Povolit přístup k virtuálnímu počítači JIT <a name="jit-configure"></a>

Přístup k virtuálnímu počítači JIT můžete povolit s vlastními možnostmi pro jeden nebo více virtuálních počítačů pomocí Security Center nebo prostřednictvím kódu programu. 

Alternativně můžete povolit JIT pomocí výchozích, pevně zakódovaných parametrů z virtuálních počítačů Azure.

Každá z těchto možností je vysvětlena na samostatné kartě níže.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Povolení JIT na virtuálních počítačích z Azure Security Center <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="Konfigurace přístupu k virtuálnímu počítači JIT v Azure Security Center":::

Z Security Center můžete povolit a nakonfigurovat přístup k virtuálnímu počítači JIT.

1. Otevřete řídicí panel Azure Defender a z oblasti Pokročilá ochrana vyberte možnost **přístup k virtuálním počítačům za běhu**.

    Otevře se stránka **přístup k virtuálnímu počítači za běhu** s vašimi virtuálními počítači na následující karty:

    - **Nakonfigurováno** – virtuální počítače, které již byly nakonfigurovány pro podporu přístupu k virtuálnímu počítači za běhu. V každém virtuálním počítači se zobrazí nakonfigurovaná karta:
        - počet schválených požadavků JIT za posledních sedm dní
        - Datum a čas posledního přístupu
        - Podrobnosti o připojení nakonfigurované
        - poslední uživatel
    - **Nenakonfigurováno** – virtuální počítače bez povoleného JIT, ale které můžou podporovat JIT. Pro tyto virtuální počítače doporučujeme povolit JIT.
    - **Nepodporované** – virtuální počítače bez povoleného JIT a které tuto funkci nepodporují. VIRTUÁLNÍ počítač může být na této kartě z následujících důvodů:
      - Chybí skupina zabezpečení sítě (NSG) – kompilátor JIT vyžaduje, aby se nakonfiguroval NSG.
      - Klasický virtuální počítač – kompilátor JIT podporuje virtuální počítače, které jsou nasazené prostřednictvím Azure Resource Manager, ne klasického nasazení. [Přečtěte si další informace o modelech nasazení Classic vs Azure Resource Manager](../azure-resource-manager/management/deployment-models.md).
      - Jiné – váš virtuální počítač může být na této kartě, pokud je řešení JIT zakázané v zásadách zabezpečení předplatného nebo skupiny prostředků.

1. Na kartě **Nenakonfigurováno** označte virtuální počítače, které chcete chránit pomocí JIT, a vyberte **Povolit JIT na virtuálních počítačích**. 

    Otevře se stránka s přístupem k virtuálnímu počítači JIT se seznamem portů, které Security Center doporučuje chránit:
    - 22. SSH
    - 3389 – RDP
    - 5985 – WinRM 
    - 5986 – WinRM

    Pokud chcete přijmout výchozí nastavení, vyberte **Uložit**.

1. Přizpůsobení možností JIT:

    - Přidejte vlastní porty pomocí tlačítka **Přidat** . 
    - Upravte jeden z výchozích portů tak, že ho vyberete ze seznamu.

    Pro každý port (vlastní a výchozí) nabízí podokno **Přidat konfiguraci portu** následující možnosti:

    - **Protokol**– protokol, který je na tomto portu povolený při schválení žádosti
    - **Povolené zdrojové IP adresy**– ROZSAHy IP adres, které jsou povolené na tomto portu při schválení žádosti
    - **Maximální doba požadavku**– maximální časový interval, po který lze otevřít konkrétní port.

     1. Nastavte zabezpečení portů podle svých potřeb.

     1. Vyberte **OK**.

1. Vyberte **Uložit**.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Úprava konfigurace JIT na virtuálním počítači s podporou JIT pomocí Security Center <a name="jit-modify"></a>

Konfiguraci za běhu virtuálního počítače můžete upravit přidáním a konfigurací nového portu, který se má chránit pro daný virtuální počítač, nebo změnou jakéhokoli jiného nastavení, které se vztahuje k již chráněnému portu.

Úprava stávajících pravidel JIT pro virtuální počítač:

1. Otevřete řídicí panel Azure Defender a z oblasti Pokročilá ochrana vyberte **Adaptivní řízení aplikací**.

1. Na kartě **konfigurované** klikněte pravým tlačítkem na virtuální počítač, ke kterému chcete přidat port, a vyberte Upravit. 

    ![Úprava konfigurace přístupu k virtuálnímu počítači JIT v Azure Security Center](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. V části **Konfigurace přístupu k virtuálním počítačům JIT**můžete upravit existující nastavení již chráněného portu nebo přidat nový vlastní port.

1. Po dokončení úprav portů vyberte **Uložit**.
 


### <a name="azure-virtual-machines"></a>[**Virtuální počítače Azure**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Povolení JIT na virtuálních počítačích z virtuálních počítačů Azure

JIT můžete povolit na virtuálním počítači na stránkách Azure Portal virtuálních počítačů Azure.

![Konfigurace přístupu k virtuálnímu počítači JIT ve virtuálních počítačích Azure](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> Pokud má virtuální počítač už povolený za běhu, zobrazí se při přechodu na jeho stránku konfigurace, že je povolený jen za běhu, a můžete použít odkaz k otevření stránky pro přístup k virtuálnímu počítači za běhu v Security Center a zobrazení a změně nastavení.

1. V [Azure Portal](https://ms.portal.azure.com)vyhledejte a vyberte **virtuální počítače**. 

1. Vyberte virtuální počítač, který chcete chránit pomocí JIT.

1. V nabídce vyberte **Konfigurace**.

1. V části **přístup za běhu**vyberte **Povolit**za běhu. 

    To umožňuje použít k virtuálnímu počítači přístup za běhu pomocí následujících výchozích nastavení:

    - Počítače s Windows:
        - Port RDP 3389
        - Tři hodiny z maximálního povoleného přístupu
        - Povolené zdrojové IP adresy jsou nastavené na any.
    - Počítače se systémem Linux:
        - Port SSH 22
        - Tři hodiny z maximálního povoleného přístupu
        - Povolené zdrojové IP adresy jsou nastavené na any.

1. Chcete-li upravit některou z těchto hodnot nebo přidat další porty do konfigurace JIT, použijte stránku za běhu Azure Security Center:

    1. V nabídce Security Center vyberte možnost **přístup k virtuálním počítačům za běhu**.

    1. Na kartě **konfigurované** klikněte pravým tlačítkem na virtuální počítač, ke kterému chcete přidat port, a vyberte Upravit. 

        ![Úprava konfigurace přístupu k virtuálnímu počítači JIT v Azure Security Center](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. V části **Konfigurace přístupu k virtuálním počítačům JIT**můžete upravit existující nastavení již chráněného portu nebo přidat nový vlastní port.

    1. Po dokončení úprav portů vyberte **Uložit**.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>Povolení JIT na virtuálních počítačích pomocí PowerShellu

Pokud chcete povolit přístup k virtuálnímu počítači za běhu z PowerShellu, použijte oficiální Azure Security Center rutinu PowerShellu `Set-AzJitNetworkAccessPolicy` .

**Příklad** – povolení přístupu k virtuálnímu počítači za běhu na konkrétním virtuálním počítači s následujícími pravidly:

* Zavřít porty 22 a 3389
* Nastavte maximální časový interval pro každé 3 hodiny, aby bylo možné je otevřít na základě schválené žádosti.
* Umožňuje uživateli, který žádá o přístup, řídit zdrojové IP adresy.
* Povolí uživateli, který žádá o přístup, aby navázal úspěšnou relaci na základě schválené žádosti o přístup za běhu.

Následující příkazy PowerShellu vytvoří tuto konfiguraci JIT:

1. Přiřaďte proměnnou, která obsahuje pravidla přístupu k virtuálnímu počítači za běhu pro virtuální počítač:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. Vložte pravidla přístupu k virtuálnímu počítači za běhu do pole:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. Nakonfigurujte pravidla přístupu k VIRTUÁLNÍm počítačům za běhu na vybraném virtuálním počítači:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    K určení virtuálního počítače použijte parametr-Name. Chcete-li například vytvořit konfiguraci JIT pro dva různé virtuální počítače, VM1 a VM2, použijte: ```Set-AzJitNetworkAccessPolicy -Name VM1``` a ```Set-AzJitNetworkAccessPolicy -Name VM2``` .


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>Povolení JIT na virtuálních počítačích pomocí REST API

Funkce přístupu k VIRTUÁLNÍm počítačům za běhu se dá použít přes rozhraní Azure Security Center API. Pomocí tohoto rozhraní API můžete získat informace o konfigurovaných virtuálních počítačích, přidat nové, požádat o přístup k virtuálnímu počítači a další. 

Další informace najdete v [zásadách síťového přístupu JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>Požádat o přístup k virtuálnímu počítači s podporou JIT

Můžete požádat o přístup k virtuálnímu počítači s podporou JIT z Azure Portal (v Security Center nebo virtuálních počítačích Azure) nebo programově.

Každá z těchto možností je vysvětlena na samostatné kartě níže.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Vyžádejte si přístup k virtuálnímu počítači s podporou JIT z Azure Security Center 

Pokud má virtuální počítač povolený kompilátor JIT, budete muset požádat o přístup, abyste se k němu mohli připojit. Přístup můžete vyžádat libovolným z podporovaných způsobů bez ohledu na to, jak jste povolili JIT.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="Vyžadování přístupu JIT z Security Center":::

1. Na stránce **přístup k virtuálnímu počítači za běhu** vyberte **nakonfigurovanou** kartu.

1. Označte virtuální počítače, ke kterým chcete získat přístup.

    - Ikona ve sloupci **Podrobnosti o připojení** označuje, zda je pro skupinu zabezpečení sítě nebo bránu firewall povolena JIT. Pokud je tato možnost povolena, zobrazí se pouze ikona brány firewall.

    - Sloupec **Podrobnosti o připojení** poskytuje informace požadované pro připojení virtuálního počítače a jeho otevřených portů.

1. Vyberte **požádat o přístup**. Otevře se okno **žádosti o přístup** .

1. V části **požádat o přístup**nakonfigurujte pro každý virtuální počítač porty, které chcete otevřít, a zdrojové IP adresy, na kterých je port otevřený, a časový interval, pro který bude port otevřený. Bude možné požádat jenom o přístup k nakonfigurovaným portům. Každý port má maximální povolený čas odvozený od konfigurace JIT, kterou jste vytvořili.

1. Vyberte **otevřít porty**.

> [!NOTE]
> Pokud je uživatel požadující přístup za proxy, možnost **moje IP adresa** nemusí fungovat. Možná budete muset definovat úplný rozsah IP adres organizace.



### <a name="azure-virtual-machines"></a>[**Virtuální počítače Azure**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Vyžádání přístupu k virtuálnímu počítači s podporou JIT ze stránky připojení virtuálního počítače Azure

Pokud má virtuální počítač povolený kompilátor JIT, budete muset požádat o přístup, abyste se k němu mohli připojit. Přístup můžete vyžádat libovolným z podporovaných způsobů bez ohledu na to, jak jste povolili JIT.

  >![požadavek za běhu JIT](./media/security-center-just-in-time/jit-request-vm.png)


Vyžádání přístupu z virtuálních počítačů Azure:

1. V Azure Portal otevřete stránky virtuálních počítačů.

1. Vyberte virtuální počítač, ke kterému se chcete připojit, a otevřete stránku **připojit** .

    Azure zkontroluje, jestli je na tomto virtuálním počítači povolená kompilátor JIT.

    - Pokud pro virtuální počítač není kompilátor JIT povolený, zobrazí se výzva k jeho povolení.

    - Pokud je technologie JIT povolena, vyberte **požádat o přístup** k předání žádosti o přístup s požadavkem na IP adresu, časový rozsah a porty, které byly nakonfigurovány pro daný virtuální počítač.

> [!NOTE]
> Po schválení žádosti o virtuální počítač chráněný serverem Azure Firewall Security Center poskytne uživateli správné podrobnosti o připojení (mapování portů z tabulky DNAT), které se použijí pro připojení k virtuálnímu počítači.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>Vyžádání přístupu k virtuálnímu počítači s podporou JIT pomocí PowerShellu

V následujícím příkladu uvidíte požadavek na přístup k virtuálnímu počítači za běhu na konkrétní virtuální počítač, ve kterém se vyžaduje otevření portu 22 pro konkrétní IP adresu a po určitou dobu:

Spusťte následující příkaz v PowerShellu:

1. Nakonfigurujte vlastnosti přístupu k žádosti virtuálních počítačů:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. Vložte parametry žádosti o přístup k virtuálnímu počítači do pole:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. Poslat žádost o přístup (použijte ID prostředku z kroku 1)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

Další informace najdete v [dokumentaci k rutinám prostředí PowerShell](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>Vyžádat přístup k virtuálním počítačům s podporou JIT pomocí REST API

Funkce přístupu k VIRTUÁLNÍm počítačům za běhu se dá použít přes rozhraní Azure Security Center API. Pomocí tohoto rozhraní API můžete získat informace o konfigurovaných virtuálních počítačích, přidat nové, požádat o přístup k virtuálnímu počítači a další. 

Další informace najdete v [zásadách síťového přístupu JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).

---








## <a name="audit-jit-access-activity-in-security-center"></a>Auditovat aktivitu přístupu JIT v Security Center

Můžete získat přehled o aktivitách virtuálních počítačů pomocí prohledávání protokolů. Postup zobrazení protokolů:

1. V případě **přístupu k virtuálnímu počítači za běhu**vyberte **nakonfigurovanou** kartu.

1. U virtuálního počítače, který chcete auditovat, otevřete v nabídce tři tečky na konci řádku.
 
1. V nabídce vyberte **Protokol aktivit** .

   ![Vybrat protokol aktivit JIT za běhu](./media/security-center-just-in-time/jit-select-activity-log.png)

   Protokol aktivit poskytuje filtrované zobrazení předchozích operací pro daný virtuální počítač společně s časem, datem a předplatným.

1. Chcete-li stáhnout informace protokolu, vyberte možnost **Stáhnout jako sdílený svazek clusteru**.








## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak nastavit a používat přístup k virtuálnímu počítači za běhu. Chcete-li zjistit, proč by se měla použít kompilátor JIT, přečtěte si článek o konceptu vysvětlující hrozby, proti kterým brání:

> [!div class="nextstepaction"]
> [Vysvětlení JIT](just-in-time-explained.md)