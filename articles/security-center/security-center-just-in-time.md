---
title: Přístup k virtuálním počítačům just-in-time ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument ukazuje, jak just-in-time přístup k virtuálním počítačům v Azure Security Center pomáhá řídit přístup k Azure virtual machines.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/2/2018
ms.author: rkarlin
ms.openlocfilehash: 6281b79e374db164bbd11b602e92336162cae089
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841757"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Správa přístupu k virtuálním počítačům pomocí just-in-time

Chcete-li zamezit příchozímu provozu na virtuální počítače Azure, tím omezit vystavení útokům při poskytování snadného přístupu pro připojení k virtuálním počítačům v případě potřeby je možné přístup k virtuálním počítačům (VM) just-in-time (JIT).

> [!NOTE]
> Funkce just-in-time je k dispozici na na úrovni Standard služby Security Center.  Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>
>

## <a name="attack-scenario"></a>Útoku

Útok hrubou silou útokům běžně cílové porty pro správu jako prostředek k získání přístupu k virtuálnímu počítači. V případě úspěchu, útočník může převzít kontrolu na virtuálním počítači a navázání proniknutí do vašeho prostředí.

Chcete-li omezit množství času, který je otevřený port je jedním ze způsobů, aby se snížila zranitelnost vůči útoku hrubou silou. Porty pro správu nemusí být otevřené nepřetržitě. Musí být otevřené pouze během připojení k virtuálnímu počítači, například kvůli provádění úloh správy nebo údržby. Pokud je povolená v čase, Security Center používá [skupinu zabezpečení sítě](../virtual-network/security-overview.md#security-rules) pravidel (NSG) omezující přístup k portům pro správu, takže nemůže být cílem útočníků.

![Scénář just-in-time](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Jak funguje přístup JIT?

Pokud je povolená v čase, Security Center uzamkne příchozí provoz do virtuálních počítačů Azure tak, že vytvoříte pravidlo skupiny zabezpečení sítě. Vyberete porty na virtuálním počítači, do které se uzamkne příchozí provoz dolů. Tyto porty se řídí řešení just-in-time.

Když uživatel požádá o přístup k virtuálnímu počítači, Security Center kontroluje, zda má uživatel [řízení přístupu na základě Role (RBAC)](../role-based-access-control/role-assignments-portal.md) oprávnění, které mohly úspěšně žádat o přístup k virtuálnímu počítači. Pokud se žádost schválí, Security Center automaticky nakonfiguruje skupiny zabezpečení sítě (Nsg), které povolí příchozí provoz na vybrané porty a požadovaná zdrojové IP adresy nebo rozsahy, množství času, který byl zadán. Po vypršení doby obnoví Security Center do předchozích stavů skupiny zabezpečení sítě. Tato připojení, které jsou už navázalo se nepřerušily, ale.

> [!NOTE]
> Security Center just-in-time virtuální počítač přístup k aktuálně podporuje pouze virtuální počítače nasazené prostřednictvím Azure Resource Manageru. Další informace o modelu nasazení classic a Resource Manageru najdete v článku [Azure Resource Manageru a klasického nasazení](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-jit-access-in-azure-security-center"></a>Pomocí přístup JIT ve službě Azure Security Center

1. Otevřete řídicí panel **Security Center**.

2. V levém podokně vyberte **Just-in-time přístup k virtuálním počítačům**.

![Dlaždice přístup virtuálních počítačů v čase](./media/security-center-just-in-time/just-in-time.png)

**Just-in-time přístup k virtuálním počítačům** otevře se okno.

![Dlaždice přístup virtuálních počítačů v čase](./media/security-center-just-in-time/just-in-time-access.png)

**Přístup k virtuálním počítačům just-in-time** poskytuje informace o stavu vašich virtuálních počítačů:

- **Nakonfigurované** – virtuální počítače, které jsou nakonfigurované pro podporu přístupu k virtuálním počítačům v čase. Dat je za poslední týden a pro každý virtuální počítač obsahuje číslo schválené žádosti, datum posledního přístupu a čas posledního uživatele.
- **Doporučené** – virtuální počítače, které podporují přístup k virtuálním počítačům v čase, ale nebyly nakonfigurovány na. Doporučujeme povolit řízení přístupu na virtuálním počítači just-in-time pro tyto virtuální počítače. Zobrazit [nakonfigurujete zásady přístupu just-in-time](#jit-config).
- **Žádné doporučení** – Mezi důvody, proč virtuální počítač nemusí být doporučený, patří:
  - Chybějící NSG – řešení just-in-time vyžaduje existenci NSG.
  - Klasický virtuální počítač – přístup k virtuálním počítačům just-in-time Security Center aktuálně podporuje pouze virtuální počítače nasazené prostřednictvím Azure Resource Manageru. Nasazení classic není podporována řešení just-in-time.
  - Jiné – virtuální počítač je v této kategorii, pokud řešení just-in-time v zásadách zabezpečení předplatného nebo skupiny prostředků, nebo v režimu hibernace, že virtuální počítač neobsahuje veřejnou IP adresu a nemá skupiny NSG na místě.

### Konfigurace zásad přístup JIT<a name="jit-config"></a>

Výběr virtuálních počítačů, které chcete povolit:

1. V části **Just-in-time přístup k virtuálním počítačům**, vyberte **doporučená** kartu.

  ![Povolení přístupu just-in-time](./media/security-center-just-in-time/enable-just-in-time-access.png)

2. V části **virtuální počítač**, vyberte virtuální počítače, které chcete povolit. To umístí značku zaškrtnutí vedle virtuálního počítače.
3. Vyberte **povolit JIT na virtuálních počítačích**.
  1. V tomto okně se zobrazí výchozí porty, doporučuje se služba Azure Security Center:
     - 22 - SSH
     - 3389 - PROTOKOL RDP
     - 5985 - WinRM 
     - 5986 - WinRM
  2. Můžete také nakonfigurovat vlastní porty. Chcete-li to provést, vyberte **přidat**. 
  3. V **přidání konfigurace portu**, pro každý z portů provedete konfiguraci i výchozí a vlastní, můžete přizpůsobit následující nastavení:
    - **Protokol typu**– protokol, který je povolen na tento port při schválení žádosti.
    - **Povolené zdrojové IP adresy**– rozsahy IP adres, které jsou povolené pro tento port při schválení žádosti.
    - **Maximální čas požadavku**– maximální časový interval, během které se dají otevřít specifického portu.

4. Vyberte **Uložit**.


> [!NOTE]
>Pokud přístup k virtuálnímu počítači JIT je povolena pro virtuální počítač, Azure Security Center vytvoří zakázat všechna pravidla pro příchozí provoz pro vybrané porty ve skupinách zabezpečení sítě s ním spojená. Pravidla budou buď skupiny zabezpečení sítě s nejvyšší prioritou, nebo nižší prioritu než mají existující pravidla, které jsou již existuje. To závisí na analýzu provádí Azure Security Center, která určuje, zda je pravidlo zabezpečení nebo ne.
>

### <a name="request-jit-access-to-a-vm"></a>Žádost o přístup JIT k virtuálnímu počítači

Chcete-li požádat o přístup k virtuálnímu počítači:
1.  V části **čas přístupu k virtuálním počítačům podle potřeby**vyberte **nakonfigurováno**.
2.  V části **virtuálních počítačů**, zkontrolujte, které chcete povolit přístup just-in-time pro virtuální počítače.
3.  Vyberte **žádat o přístup**. 
  ![žádost o přístup k virtuálnímu počítači](./media/security-center-just-in-time/request-access-to-a-vm.png)
4.  V části **žádat o přístup**, pro každý virtuální počítač konfigurovat porty, které chcete otevřít, a zdrojové IP adresy, které port, který se otevírá ve službě a časový interval, pro který port bude otevřete. Pouze bude možné požádat o přístup k portům, které jsou nakonfigurované v zásadách just-in-time. Každý z portů má maximální povolenou dobu odvozené ze zásad just-in-time.
5.  Vyberte **otevřít porty**.

> [!NOTE]
> Pokud uživatel, který žádá o přístup je za proxy serverem, možnost **moji IP adresu** nemusí fungovat. Budete muset definovat úplný rozsah IP adres organizace.

### <a name="editing-a-jit-access-policy"></a>Úpravy zásad přístup JIT

Existující zásady just-in-time Virtuálního počítače můžete změnit přidáním a nakonfigurování nový port pro ochranu pro tento virtuální počítač nebo změnou jakékoli další nastavení související s již chráněné portu.

Chcete-li upravit existující zásadu just-in-time virtuálního počítače:
1. V **nakonfigurováno** ve skupině **virtuálních počítačů**, vyberte virtuální počítač, do které chcete přidat portu pro tento virtuální počítač kliknutím na tři tečky v rámci řádku. 
2. Vyberte **Upravit**.
3. V části **konfigurace přístupu k virtuálnímu počítači podle potřeby**, můžete upravit stávající nastavení už chráněných portu nebo přidat nový port. Tento vlastní port. Další informace najdete v tématu [nakonfigurujete zásady přístupu just-in-time](#jit-config). 
  ![přístup k virtuálnímu počítači JIT](./media/security-center-just-in-time/edit-policy.png)

## <a name="using-jit-access-in-an-azure-vm-blade"></a>V okně virtuálního počítače Azure pomocí přístup JIT

Pro usnadnění práce můžete připojit k virtuálnímu počítači pomocí JIT přímo z v rámci okna virtuálního počítače v Azure.

### <a name="configuring-a-just-in-time-access-policy"></a>Konfigurace zásady přístupu just-in-time 
Abyste usnadnili snadnou zavedení přístup za běhu na vašich virtuálních počítačích, můžete nastavit virtuální počítač povolit pouze just-in-time přístup přímo z v rámci virtuálního počítače.

1. Na webu Azure Portal, vyberte **virtuálních počítačů**.
2. Klikněte na virtuální počítač, který chcete omezit přístup just-in-time.
3. V nabídce klikněte na tlačítko **konfigurace**.
4. V části **pouze v čase přístupu** klikněte na tlačítko **just-in-time zásadu Povolit**. 

To umožňuje přístup just-in-time pro virtuální počítač pomocí následujících nastavení:

- Windows servery:
    - Port 3389 pro RDP
    - maximální povolené přístupu 3 hodiny
    - Povolené zdrojové IP adresy je nastavena na každý požadavek
- Servery s Linuxem:
    - SSH port 22
    - maximální povolené přístupu 3 hodiny
    - Povolené zdrojové IP adresy je nastavena na každý požadavek
     
Pokud je virtuální počítač už just-in-time povolena, když přejdete na stránku jeho konfigurace bude možné zjistit, že je povolená v čase a odkaz můžete otevřít ve službě Azure Security Center k zobrazení a změna nastavení zásad.

![Konfigurace JIT ve virtuálním počítači](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="requesting-jit-access-to-a-vm"></a>Žádost o přístup JIT k virtuálnímu počítači

Na webu Azure Portal při pokusu o připojení k virtuálnímu počítači, zkontroluje Azure a zjistěte, jestli máte přístup just-in-time zásady nakonfigurované na tomto virtuálním počítači.

- Pokud nemáte nakonfigurovaný na virtuálním počítači JIT, zobrazí výzva ke konfiguraci zásad JIT ho.

  ![řádek JIT](./media/security-center-just-in-time/jit-prompt.png)

- Pokud máte zásadu JIT nakonfigurovaný na virtuálním počítači, můžete kliknout na **žádat o přístup** povolit přístup v souladu se zásadami JIT nastavit pro virtuální počítač.

  ![žádost o přístup JIT](./media/security-center-just-in-time/jit-request-access.png)

## <a name="auditing-jit-access-activity"></a>Auditování aktivity přístup JIT

Můžete získat přehled o aktivitách virtuálního počítače pomocí prohledávání protokolů. Chcete-li zobrazit protokoly:

1. V části **Just-in-time přístup k virtuálním počítačům**, vyberte **nakonfigurováno** kartu.
2. V části **virtuálních počítačů**, vyberte virtuální počítač k zobrazení informací o po kliknutí na tři tečky v rámci řádku pro tento virtuální počítač. Otevře se nabídka.
3. Vyberte **protokolu aktivit** v nabídce. Tím se otevře **protokolu aktivit**.

  ![Vyberte protokol aktivit](./media/security-center-just-in-time/select-activity-log.png)

  **Protokol aktivit** poskytuje filtrované zobrazení předchozích operací pro tento virtuální počítač spolu s čas, datum a předplatné.

Informace protokolu můžete stáhnout tak, že vyberete **kliknutím sem stáhnete všechny položky jako CSV**.

Upravit filtry a vybrat **použít** vytvoříte vyhledávání a protokolu.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Oprávnění potřebná ke konfiguraci a použití JIT

Nastavte tyto požadovaná oprávnění a povolit tak uživateli konfigurovat nebo úprava zásad JIT pro virtuální počítač.

Přiřadit tato *akce* do role: 
-   V oboru předplatného nebo skupiny prostředků, které souvisí s virtuálním Počítačem:
   - Microsoft.Security/locations/jitNetworkAccessPolicies/write
-    V oboru předplatného nebo skupiny prostředků nebo virtuálního počítače:
   - Microsoft.Compute/virtualMachines/write 

Nastavte tato oprávnění a povolit tak uživateli úspěšně požádat o přístup JIT k virtuálnímu počítači: přiřadit tato *akce* uživateli:
-   V oboru předplatného nebo skupiny prostředků, které souvisí s virtuálním Počítačem:
   - Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/ zahájení nebo akce
-    V oboru předplatného nebo skupiny prostředků nebo virtuálního počítače:
   - Microsoft.Compute/virtualMachines/read



## <a name="use-jit-programmatically"></a>Pomocí JIT prostřednictvím kódu programu
Můžete nastavit a použít just-in-time přes rozhraní REST API a pomocí Powershellu.

### <a name="using-just-in-time-vm-access-via-rest-apis"></a>Použití just-in-time přístup k virtuálním počítačům přes rozhraní REST API

Funkce just-in-time virtuální počítač přístup je možné prostřednictvím rozhraní API služby Azure Security Center. Můžete získat informace o nakonfigurovaných virtuálních počítačů, přidat nové, požádejte o přístup k virtuálnímu počítači a další, přes toto rozhraní API. Zobrazit [zásad přístupu k síti Jit](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies), další informace o rozhraní REST API just-in-time.

### <a name="using-jit-vm-access-via-powershell"></a>Pomocí přístupu k virtuálnímu počítači podle potřeby přes PowerShell 

Pokud chcete používat řešení just-in-time virtuálních počítačů přístup prostřednictvím Powershellu, použijte oficiální rutiny prostředí PowerShell Azure Security Center a to konkrétně `Set-AzureRmJitNetworkAccessPolicy`.

Následující příklad nastaví zásadu přístupu za běhu virtuálního počítače na konkrétní virtuální počítač a nastaví následující:
1.  Zavřít porty 22 a 3389.
2.  Nastavte maximální časový interval pro každé 3 hodiny, lze je otevřít na schválení žádosti.
3.  Umožňuje uživateli, který žádá o přístup k řídícímu zdrojové IP adresy a umožňuje uživateli vytvořit relaci úspěšné při žádosti o přístup schválené just-in-time.

Spuštěním následujícího příkazu v Powershellu k provedení této:

1.  Přiřazení proměnné, který obsahuje zásady přístupu za běhu virtuálního počítače pro virtuální počítač:

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

2.  Vložte zásady přístupu virtuálních počítačů just-in-time virtuálního počítače do pole:
    
        $JitPolicyArr=@($JitPolicy)

3.  Konfigurace zásady přístupu za běhu virtuálního počítače na vybraný virtuální počítač:
    
        Set-AzureRmJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="requesting-access-to-a-vm"></a>Žádost o přístup k virtuálnímu počítači

V následujícím příkladu zobrazí se žádost o just-in-time virtuální počítač přístup na konkrétní virtuální počítač, ve kterém portu 22 požaduje otevřít pro konkrétní IP adresy a pro určité množství času:

Spuštěním následujícího příkazu v Powershellu:
1.  Konfigurace vlastností virtuálního počítače žádost o přístup

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Vložte parametry žádosti o přístup virtuálního počítače v poli:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Odeslat žádost o přístup (použijte ID prostředku v jste získali v kroku 1)

        Start-AzureRmJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Další informace najdete v článku dokumentace k rutinám prostředí PowerShell.


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak just-in-time přístup k virtuálním počítačům v Security Center pomáhá řídit přístup k Azure virtual machines.

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

- [Nastavení zásad zabezpečení](security-center-azure-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Správa doporučení zabezpečení](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
- [Sledování stavu zabezpečení](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
- [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reagovat na výstrahy zabezpečení.
- [Sledování partnerských řešení](security-center-partner-solutions.md) – zjistěte, jak sledovat stav vašich partnerských řešení.
- [Security Center – nejčastější dotazy](security-center-faq.md) – přečtěte si nejčastější dotazy k používání této služby.
- [Blog o zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

