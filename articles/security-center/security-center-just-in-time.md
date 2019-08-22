---
title: Přístup k virtuálnímu počítači za běhu v Azure Security Center | Microsoft Docs
description: Tento dokument ukazuje, jak přístup k virtuálnímu počítači za běhu v Azure Security Center pomáhá řídit přístup k virtuálním počítačům Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 671930b1-fc84-4ae2-bf7c-d34ea37ec5c7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: f3e6cc0464c8f395db7cac0ebf8a16230f5ebcbe
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872917"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Správa přístupu k virtuálnímu počítači pomocí za běhu

Přístup k virtuálnímu počítači za běhu (just-in-time) se dá použít k uzamknutí příchozího provozu do virtuálních počítačů Azure. tím se sníží riziko útoků na útoky a zároveň se vám umožní snadný přístup k virtuálním počítačům v případě potřeby.

> [!NOTE]
> Funkce za běhu je k dispozici na úrovni Standard Security Center.  Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).


> [!NOTE]
> Přístup k virtuálnímu počítači za běhu Security Center aktuálně podporuje jenom virtuální počítače nasazené prostřednictvím Azure Resource Manager. Další informace o modelech nasazení Classic a Správce prostředků najdete v tématu [Azure Resource Manager vs. Classic Deployment](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="attack-scenario"></a>Scénář útoku

Útoky hrubou silou běžně cílí na porty správy jako prostředky pro získání přístupu k virtuálnímu počítači. V případě úspěchu může útočník převzít kontrolu nad VIRTUÁLNÍm počítačem a vytvořit dostane do vašeho prostředí.

Jedním ze způsobů, jak omezit vystavení útokem hrubou silou, je omezit dobu, po kterou je port otevřený. Porty pro správu nemusí být otevřené nepřetržitě. Musí být otevřené pouze během připojení k virtuálnímu počítači, například kvůli provádění úloh správy nebo údržby. Pokud je povolený program just-in-time, Security Center používá pravidla [skupiny zabezpečení sítě](../virtual-network/security-overview.md#security-rules) (NSG) a Azure firewall, která omezují přístup k portům pro správu, aby na ně nedokázali zacílit útočníci.

![Scénář za běhu](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Jak funguje přístup JIT?

Když je povolený postup za běhu, Security Center zamkne příchozí provoz do vašich virtuálních počítačů Azure vytvořením pravidla NSG. Vyberete porty na virtuálním počítači, pro které bude příchozí provoz uzamčen. Tyto porty jsou ovládány pomocí řešení za běhu.

Když si uživatel vyžádá přístup k virtuálnímu počítači, Security Center zkontroluje, jestli má uživatel oprávnění [Access Control na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md) , které jim umožní úspěšně požádat o přístup k virtuálnímu počítači. Pokud je žádost schválená, Security Center automaticky nakonfiguruje skupiny zabezpečení sítě (skupin zabezpečení sítě) a Azure Firewall tak, aby povolovaly příchozí provoz na vybraných portech a požadované zdrojové IP adresy nebo rozsahy po dobu, kterou jste zadali. Po vypršení časového limitu Security Center obnoví skupin zabezpečení sítě do jejich předchozích stavů. Připojení, která jsou již navázána, se však přeruší.

 > [!NOTE]
 > Pokud je žádost o přístup JIT schválená pro virtuální počítač za Azure Firewall, Security Center automaticky změní obě pravidla zásad NSG i brány firewall. V době, kterou jste zadali, budou pravidla umožňovat příchozí provoz na vybrané porty a požadované zdrojové IP adresy nebo rozsahy. Po uplynutí této doby Security Center obnoví v předchozích stavech pravidla brány firewall a NSG.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Oprávnění potřebná ke konfiguraci a použití JIT

| Umožnění uživateli: | Oprávnění k nastavení|
| --- | --- |
| Konfigurace nebo úprava zásad JIT pro virtuální počítač | *Přiřaďte k roli tyto akce:*  <ul><li>V oboru předplatného nebo skupiny prostředků, která je přidružená k virtuálnímu počítači:<br/> ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` </li><li> V oboru předplatného nebo skupiny prostředků nebo virtuálního počítače: <br/>```Microsoft.Compute/virtualMachines/write```</li></ul> | 
| ||
|Vyžádat přístup JIT k virtuálnímu počítači | *Přiřaďte uživatele k těmto akcím:*  <ul><li>V oboru předplatného nebo skupiny prostředků, která je přidružená k virtuálnímu počítači:<br/>  ```Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action``` </li><li>  V oboru předplatného nebo skupiny prostředků nebo virtuálního počítače:<br/> ```Microsoft.Compute/virtualMachines/read``` </li></ul>|


## <a name="configure-jit-on-a-vm"></a>Konfigurace JIT na virtuálním počítači

Existují tři způsoby, jak nakonfigurovat zásady JIT na virtuálním počítači:

- [Konfigurace přístupu JIT v Azure Security Center](#jit-asc)
- [Konfigurace přístupu JIT v okně virtuálního počítače Azure](#jit-vm)
- [Programové nakonfigurování zásad JIT na virtuálním počítači](#jit-program)

## <a name="configure-jit-in-asc"></a>Konfigurace JIT v ASC

Od ASC můžete nakonfigurovat zásady JIT a požádat o přístup k virtuálnímu počítači pomocí zásad JIT.


### Konfigurace přístupu JIT na virtuálním počítači v ASC<a name="jit-asc"></a>

1. Otevřete řídicí panel **Security Center**.

2. V levém podokně vyberte **přístup k virtuálnímu počítači za běhu**.

    ![Dlaždice přístupu k virtuálnímu počítači za běhu](./media/security-center-just-in-time/just-in-time.png)

    Otevře se okno **přístup k virtuálnímu počítači za běhu** .

      ![Povolit přístup za běhu](./media/security-center-just-in-time/enable-just-in-time.png)

    **Přístup k virtuálnímu počítači za běhu** poskytuje informace o stavu virtuálních počítačů:

    - **Nakonfigurováno** – virtuální počítače, které byly nakonfigurovány pro podporu přístupu k virtuálnímu počítači za běhu. Uvedená data jsou za poslední týden a obsahují pro každý virtuální počítač počet schválených požadavků, datum posledního přístupu a čas a poslední uživatel.
    - **Doporučené** – virtuální počítače, které můžou podporovat přístup k virtuálním počítačům za běhu, ale nejsou nakonfigurované na. Pro tyto virtuální počítače doporučujeme povolit řízení přístupu k virtuálnímu počítači za běhu. 
    - **Žádné doporučení** – Mezi důvody, proč virtuální počítač nemusí být doporučený, patří:
      - Chybějící NSG – řešení za běhu vyžaduje, aby bylo na místě NSG.
      - Klasický přístup k VIRTUÁLNÍm počítačům (VM) – Security Center za běhu aktuálně podporuje jenom virtuální počítače nasazené prostřednictvím Azure Resource Manager. Řešení za běhu nepodporuje klasické nasazení. 
      - Jiné – virtuální počítač je v této kategorii, pokud je řešení za běhu vypnuto v zásadách zabezpečení předplatného nebo skupiny prostředků, nebo pokud virtuální počítač nemá veřejnou IP adresu a NSG na něj.

3. Vyberte kartu **Doporučené** .

4. V části **virtuální počítač**klikněte na virtuální počítače, které chcete povolit. Tím se zaškrtne políčko vedle virtuálního počítače.

5. Klikněte na **Povolit JIT na virtuálních počítačích**.
   -. V tomto okně se zobrazují výchozí porty doporučené aplikací Azure Security Center:
      - 22 - SSH
      - 3389 – RDP
      - 5985 – WinRM 
      - 5986 – WinRM
6. Můžete taky nakonfigurovat vlastní porty:

      1. Klikněte na **Přidat**. Otevře se okno **Přidat konfiguraci portu** .
      2. Pro každý port, který se rozhodnete nakonfigurovat, můžete nastavit výchozí i vlastní, abyste mohli přizpůsobit následující nastavení:

    - **Typ protokolu**– protokol, který je na tomto portu povolený, když je žádost schválená.
    - **Povolené zdrojové IP adresy**– ROZSAHy IP adres, které jsou na tomto portu povolené, když je žádost schválená.
    - **Maximální doba požadavku**– maximální časový interval, během kterého lze otevřít konkrétní port.

     3. Klikněte na **OK**.

1. Klikněte na **Uložit**.

> [!NOTE]
>Když je pro virtuální počítač povolený přístup k virtuálnímu počítači JIT, Azure Security Center vytvoří pravidla odepřít veškerý příchozí provoz pro vybrané porty ve skupinách zabezpečení sítě, které jsou k ní přidružené a Azure Firewall. Pokud se pro vybrané porty vytvořila jiná pravidla, stávající pravidla mají přednost před novými pravidly odepřít všechna příchozí provoz. Pokud na vybraných portech neexistují žádná pravidla, nová pravidla odepřít všechna příchozí přenosy mají ve skupinách zabezpečení sítě a Azure Firewall nejvyšší prioritu.


## <a name="request-jit-access-via-asc"></a>Vyžádat přístup JIT pomocí ASC

Vyžádání přístupu k virtuálnímu počítači prostřednictvím ASC:

1. V části **přístup k virtuálnímu počítači**podle potřeby vyberte **nakonfigurovanou** kartu.

2. V části **virtuální počítač**klikněte na virtuální počítače, pro které chcete požádat o přístup. Tím se zaškrtne políčko vedle virtuálního počítače.


    - Ikona ve sloupci **Podrobnosti o připojení** označuje, zda je v NSG nebo FW povolena JIT. Pokud je tato možnost povolena, zobrazí se pouze ikona brány firewall.

    - Sloupec **Podrobnosti o připojení** poskytuje správné informace potřebné k připojení virtuálního počítače a také označuje otevřené porty.

      ![Vyžádání přístupu za běhu](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Klikněte na **požádat o přístup**. Otevře se okno **žádosti o přístup** .

      ![Podrobnosti JIT](./media/security-center-just-in-time/just-in-time-details.png)

4. V části **požádat o přístup**nakonfigurujte pro každý virtuální počítač porty, které chcete otevřít, a zdrojové IP adresy, na kterých je port otevřený, a časový interval, pro který bude port otevřený. Bude možné požádat jenom o přístup k portům, které jsou nakonfigurované v zásadě za běhu. Každý port má maximální povolený čas odvozený ze zásad za běhu.

5. Klikněte na **otevřít porty**.

> [!NOTE]
> Pokud je uživatel požadující přístup za proxy, možnost **moje IP adresa** nemusí fungovat. Možná budete muset definovat úplný rozsah IP adres organizace.

## <a name="edit-a-jit-access-policy-via-asc"></a>Úprava zásad přístupu JIT prostřednictvím ASC

Existující zásady za běhu virtuálního počítače můžete změnit tak, že přidáte a nakonfigurujete nový port pro ochranu tohoto virtuálního počítače nebo změníte jakékoli jiné nastavení týkající se již chráněného portu.

Postup úpravy existujících zásad za běhu virtuálního počítače:
1. Na kartě **konfigurované** v části **virtuální počítače**vyberte virtuální počítač, ke kterému chcete přidat port kliknutím na tři tečky v řádku tohoto virtuálního počítače. 

1. Vyberte **Upravit**.
1. V části **Konfigurace přístupu k virtuálním počítačům JIT**můžete upravit existující nastavení již chráněného portu nebo přidat nový vlastní port. 
  ![přístup k virtuálnímu počítači JIT](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-asc"></a>Auditovat aktivitu přístupu JIT v ASC

Můžete získat přehled o aktivitách virtuálních počítačů pomocí prohledávání protokolů. Postup zobrazení protokolů:

1. V části **přístup k virtuálnímu počítači za běhu**vyberte **nakonfigurovanou** kartu.
2. V části **virtuální počítače**vyberte virtuální počítač, pro který chcete zobrazit informace. stačí kliknout na tři tečky v řádku daného virtuálního počítače a v nabídce vybrat **Protokol aktivit** . Otevře se **Protokol aktivit** .

   ![Vybrat protokol aktivit](./media/security-center-just-in-time/select-activity-log.png)

   **Protokol aktivit** poskytuje filtrované zobrazení předchozích operací pro daný virtuální počítač společně s časem, datem a předplatným.

Informace o protokolu si můžete stáhnout tak, že vyberete **kliknutím sem stáhnete všechny položky jako CSV**.

Upravte filtry a kliknutím na **použít** vytvořte hledání a protokol.



## Konfigurace přístupu JIT v okně virtuálního počítače Azure<a name="jit-vm"></a>

Pro usnadnění práce se můžete připojit k virtuálnímu počítači pomocí JIT přímo v okně virtuálního počítače v Azure.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-blade"></a>Konfigurace přístupu JIT na virtuálním počítači přes okno virtuálního počítače Azure

Aby bylo možné v rámci virtuálních počítačů snadno naprogramovat přístup za běhu, můžete nastavit virtuální počítač tak, aby umožňoval pouze přístup za běhu přímo z virtuálního počítače.

1. V Azure Portal vyberte **virtuální počítače**.
2. Klikněte na virtuální počítač, který chcete omezit na přístup za běhu.
3. V nabídce klikněte na položku **Konfigurace**.
4. V části **přístup za běhu** klikněte na **Povolit zásady**za běhu. 

To umožňuje použít pro virtuální počítač přístup za běhu pomocí následujících nastavení:

- Servery Windows:
    - Port RDP 3389
    - 3 hodiny z maximálního povoleného přístupu
    - Povolené zdrojové IP adresy jsou nastavené na any.
- Servery Linux:
    - Port SSH 22
    - 3 hodiny z maximálního povoleného přístupu
    - Povolené zdrojové IP adresy jsou nastavené na any.
     
Pokud má virtuální počítač už povolený just-in-time, budete při přechodu na jeho stránku konfigurace moct zjistit, jestli je povolený za běhu, a můžete použít odkaz k otevření zásady v Azure Security Center k zobrazení a změně nastavení.

![Konfigurace JIT ve virtuálním počítači](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Podání žádosti o přístup JIT k virtuálnímu počítači přes okno Azure VM

Když se v Azure Portal pokusíte připojit k virtuálnímu počítači, Azure zkontroluje, jestli máte na daném virtuálním počítači nakonfigurovanou zásadu přístupu za běhu.

- Pokud máte na virtuálním počítači nakonfigurovanou zásadu JIT, můžete kliknout na **požádat o přístup** , abyste vám povolili přístup v souladu se sadou zásad JIT nastavenou pro daný virtuální počítač. 

  >![požadavek JIT](./media/security-center-just-in-time/jit-request.png)

  Přístup je požadován s následujícími výchozími parametry:

  - **zdrojová IP adresa**: Any (*) (nejde změnit)
  - **časový rozsah**: 3 hodiny (nejde změnit)  <!--Isn't this set in the policy-->
  - **číslo portu** Port RDP 3389 pro Windows/port 22 pro Linux (lze změnit)

    > [!NOTE]
    > Po schválení žádosti o virtuální počítač chráněný serverem Azure Firewall Security Center poskytne uživateli správné podrobnosti o připojení (mapování portů z tabulky DNAT), které se použijí pro připojení k virtuálnímu počítači.

- Pokud na virtuálním počítači není kompilátor JIT nakonfigurovaný, budete vyzváni ke konfiguraci zásad JIT.

  ![příkazový řádek JIT](./media/security-center-just-in-time/jit-prompt.png)

## Programové nakonfigurování zásad JIT na virtuálním počítači<a name="jit-program"></a>

Pomocí rozhraní REST API a prostředí PowerShell můžete nastavit a použít za běhu.

## <a name="jit-vm-access-via-rest-apis"></a>Přístup k virtuálnímu počítači JIT přes rozhraní REST API

Funkce přístupu k VIRTUÁLNÍm počítačům za běhu se dá použít přes rozhraní Azure Security Center API. Pomocí tohoto rozhraní API můžete získat informace o konfigurovaných virtuálních počítačích, přidat nové, vyžadovat přístup k virtuálnímu počítači a další. Další informace o REST API pro dobu běhu najdete v tématu [zásady přístupu k síti JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).

## <a name="jit-vm-access-via-powershell"></a>Přístup k virtuálnímu počítači JIT prostřednictvím PowerShellu

Pokud chcete použít řešení přístupu k virtuálnímu počítači podle potřeby přes PowerShell, použijte oficiální rutiny Azure Security Center PowerShellu a konkrétně `Set-AzJitNetworkAccessPolicy`.

Následující příklad nastaví zásady přístupu k VIRTUÁLNÍm počítačům za běhu na konkrétní virtuální počítač a nastaví následující:

1.  Zavřete porty 22 a 3389.

2.  Nastavte maximální časový interval pro každé 3 hodiny, aby bylo možné je otevřít na základě schválené žádosti.
3.  Umožňuje uživateli, který žádá o přístup k řízení zdrojových IP adres, a umožňuje uživateli vytvořit úspěšnou relaci v rámci schválené žádosti o přístup za běhu.

K tomu použijte následující příkaz v PowerShellu:

1.  Přiřaďte proměnnou, která obsahuje zásady přístupu k VIRTUÁLNÍm počítačům za běhu pro virtuální počítač:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Do pole vložte zásady přístupu k virtuálnímu počítači za běhu podle potřeby:
    
        $JitPolicyArr=@($JitPolicy)

3.  Nakonfigurujte zásady přístupu k VIRTUÁLNÍm počítačům za běhu na vybraném virtuálním počítači:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>Vyžádat přístup k virtuálnímu počítači přes PowerShell

V následujícím příkladu uvidíte požadavek na přístup k virtuálnímu počítači za běhu na konkrétní virtuální počítač, ve kterém se vyžaduje otevření portu 22 pro konkrétní IP adresu a po určitou dobu:

Spusťte následující příkaz v PowerShellu:
1.  Konfigurovat vlastnosti přístupu k žádosti virtuálních počítačů

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Vložte parametry žádosti o přístup k virtuálnímu počítači do pole:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Poslat žádost o přístup (použijte ID prostředku, které jste získali v kroku 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Další informace najdete v dokumentaci k rutinám PowerShellu.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak přístup k virtuálnímu počítači za běhu v Security Center pomáhá řídit přístup k virtuálním počítačům Azure.

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

- [Nastavení zásad zabezpečení](tutorial-security-policy.md) – Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Správa doporučení zabezpečení](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
- [Sledování stavu zabezpečení](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
- [Správa a reakce na výstrahy zabezpečení](security-center-managing-and-responding-alerts.md) – Naučte se spravovat výstrahy zabezpečení a reagovat na ně.
- [Monitorování partnerských řešení](security-center-partner-solutions.md) : Naučte se monitorovat stav vašich partnerských řešení.
- [Nejčastější](security-center-faq.md) dotazy k Security Center – Přečtěte si nejčastější dotazy týkající se používání služby.
- [Blog o zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

