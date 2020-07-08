---
title: Přístup k virtuálnímu počítači za běhu v Azure Security Center | Microsoft Docs
description: Tento dokument ukazuje, jak přístup k virtuálnímu počítači za běhu v Azure Security Center pomáhá řídit přístup k virtuálním počítačům Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: b24e0487aef73ed7852cb4a64766a1f8d92aff94
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677423"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Zabezpečení portů pro správu pomocí přístupu za běhu

Pokud používáte cenovou úroveň Standard Security Center (viz [ceny](/azure/security-center/security-center-pricing)), můžete na virtuální počítače Azure uzamknout příchozí provoz s přístupem JIT (just-in-time) k virtuálnímu počítači (VM). Tím se snižuje riziko útoků při poskytování snadného přístupu pro připojení k virtuálním počítačům v případě potřeby.

> [!NOTE]
> Přístup k virtuálnímu počítači za běhu Security Center aktuálně podporuje jenom virtuální počítače nasazené prostřednictvím Azure Resource Manager. Další informace o modelech nasazení Classic a Správce prostředků najdete v tématu [Azure Resource Manager vs. Classic Deployment](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Konfigurace JIT na virtuálním počítači

Existují tři způsoby, jak nakonfigurovat zásady JIT na virtuálním počítači:

- [Konfigurace přístupu JIT v Azure Security Center](#jit-asc)
- [Konfigurace přístupu JIT na stránce virtuálního počítače Azure](#jit-vm)
- [Programové nakonfigurování zásad JIT na virtuálním počítači](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Konfigurace JIT v Azure Security Center

Z Security Center můžete nakonfigurovat zásady JIT a požádat o přístup k virtuálnímu počítači pomocí zásad JIT.

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>Konfigurace přístupu JIT na virtuálním počítači v Security Center<a name="jit-asc"></a>

1. Otevřete řídicí panel **Security Center**.

1. V levém podokně vyberte **přístup k virtuálnímu počítači za běhu**.

    ![Dlaždice přístupu k virtuálnímu počítači za běhu](./media/security-center-just-in-time/just-in-time.png)

    Otevře se okno **přístup k virtuálnímu počítači za běhu** , ve kterém se zobrazí informace o stavu virtuálních počítačů:

    - **Nakonfigurováno** – virtuální počítače, které byly nakonfigurovány pro podporu přístupu k virtuálnímu počítači za běhu. Uvedená data jsou za poslední týden a obsahují pro každý virtuální počítač počet schválených požadavků, datum posledního přístupu a čas a poslední uživatel.
    - **Doporučené** – virtuální počítače, které můžou podporovat přístup k virtuálním počítačům za běhu, ale nejsou nakonfigurované na. Pro tyto virtuální počítače doporučujeme povolit řízení přístupu k virtuálnímu počítači za běhu.
    - **Žádné doporučení** – Mezi důvody, proč virtuální počítač nemusí být doporučený, patří:
      - Chybějící NSG – řešení za běhu vyžaduje, aby bylo na místě NSG.
      - Klasický přístup k VIRTUÁLNÍm počítačům (VM) – Security Center za běhu aktuálně podporuje jenom virtuální počítače nasazené prostřednictvím Azure Resource Manager. Řešení za běhu nepodporuje klasické nasazení. 
      - Jiné – virtuální počítač je v této kategorii, pokud je řešení za běhu vypnuto v zásadách zabezpečení předplatného nebo skupiny prostředků, nebo pokud virtuální počítač nemá veřejnou IP adresu a NSG na něj.

1. Vyberte kartu **Doporučené** .

1. V části **virtuální počítač**klikněte na virtuální počítače, které chcete povolit. Tím se zaškrtne políčko vedle virtuálního počítače.

      ![Povolit přístup za běhu](./media/security-center-just-in-time/enable-just-in-time.png)

1. Klikněte na **Povolit JIT na virtuálních počítačích**. Otevře se podokno, ve kterém se zobrazí výchozí porty Doporučené Azure Security Center:
    - 22. SSH
    - 3389 – RDP
    - 5985 – WinRM 
    - 5986 – WinRM
1. Volitelně můžete do seznamu přidat vlastní porty:

      1. Klikněte na tlačítko **Add** (Přidat). Otevře se okno **Přidat konfiguraci portu** .
      1. Pro každý port, který se rozhodnete nakonfigurovat, můžete nastavit výchozí i vlastní, abyste mohli přizpůsobit následující nastavení:
            - **Typ protokolu**– protokol, který je na tomto portu povolený, když je žádost schválená.
            - **Povolené zdrojové IP adresy**– ROZSAHy IP adres, které jsou na tomto portu povolené, když je žádost schválená.
            - **Maximální doba požadavku**– maximální časový interval, během kterého lze otevřít konkrétní port.

     1. Klikněte na **OK**.

1. Klikněte na **Uložit**.

> [!NOTE]
>Když je pro virtuální počítač povolený přístup k virtuálnímu počítači JIT, Azure Security Center vytvoří pravidla odepřít veškerý příchozí provoz pro vybrané porty ve skupinách zabezpečení sítě, které jsou k ní přidružené a Azure Firewall. Pokud se pro vybrané porty vytvořila jiná pravidla, stávající pravidla mají přednost před novými pravidly odepřít všechna příchozí provoz. Pokud na vybraných portech neexistují žádná pravidla, nová pravidla odepřít všechna příchozí přenosy mají ve skupinách zabezpečení sítě a Azure Firewall nejvyšší prioritu.


## <a name="request-jit-access-via-security-center"></a>Vyžádat přístup JIT pomocí Security Center

Vyžádání přístupu k virtuálnímu počítači prostřednictvím Security Center:

1. V části **přístup k virtuálnímu počítači za běhu**vyberte **nakonfigurovanou** kartu.

1. V části **virtuální počítač**klikněte na virtuální počítače, pro které chcete požádat o přístup. Tím se zaškrtne políčko vedle virtuálního počítače.

    - Ikona ve sloupci **Podrobnosti o připojení** označuje, zda je v NSG nebo FW povolena JIT. Pokud je tato možnost povolena, zobrazí se pouze ikona brány firewall.

    - Sloupec **Podrobnosti o připojení** poskytuje informace požadované pro připojení virtuálního počítače a jeho otevřených portů.

      ![Vyžádání přístupu za běhu](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Klikněte na **požádat o přístup**. Otevře se okno **žádosti o přístup** .

      ![Podrobnosti JIT](./media/security-center-just-in-time/just-in-time-details.png)

1. V části **požádat o přístup**nakonfigurujte pro každý virtuální počítač porty, které chcete otevřít, a zdrojové IP adresy, na kterých je port otevřený, a časový interval, pro který bude port otevřený. Bude možné požádat jenom o přístup k portům, které jsou nakonfigurované v zásadě za běhu. Každý port má maximální povolený čas odvozený ze zásad za běhu.

1. Klikněte na **otevřít porty**.

> [!NOTE]
> Pokud je uživatel požadující přístup za proxy, možnost **moje IP adresa** nemusí fungovat. Možná budete muset definovat úplný rozsah IP adres organizace.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Úprava zásad přístupu JIT prostřednictvím Security Center

Existující zásady za běhu virtuálního počítače můžete změnit tak, že přidáte a nakonfigurujete nový port pro ochranu tohoto virtuálního počítače nebo změníte jakékoli jiné nastavení týkající se již chráněného portu.

Postup úpravy existujících zásad za běhu virtuálního počítače:

1. Na kartě **konfigurované** v části **virtuální počítače**vyberte virtuální počítač, ke kterému chcete přidat port kliknutím na tři tečky v řádku tohoto virtuálního počítače. 

1. Vyberte **Upravit**.

1. V části **Konfigurace přístupu k virtuálním počítačům JIT**můžete upravit existující nastavení již chráněného portu nebo přidat nový vlastní port. 
  ![přístup k virtuálnímu počítači JIT](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Auditovat aktivitu přístupu JIT v Security Center

Můžete získat přehled o aktivitách virtuálních počítačů pomocí prohledávání protokolů. Postup zobrazení protokolů:

1. V části **přístup k virtuálnímu počítači za běhu**vyberte **nakonfigurovanou** kartu.
2. V části **virtuální počítače**vyberte virtuální počítač, pro který chcete zobrazit informace. stačí kliknout na tři tečky v řádku daného virtuálního počítače a v nabídce vybrat **Protokol aktivit** . Otevře se **Protokol aktivit** .

   ![Vybrat protokol aktivit](./media/security-center-just-in-time/select-activity-log.png)

   **Protokol aktivit** poskytuje filtrované zobrazení předchozích operací pro daný virtuální počítač společně s časem, datem a předplatným.

Informace o protokolu si můžete stáhnout tak, že vyberete **kliknutím sem stáhnete všechny položky jako CSV**.

Upravte filtry a kliknutím na **použít** vytvořte hledání a protokol.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>Konfigurace přístupu JIT ze stránky virtuálního počítače Azure<a name="jit-vm"></a>

Pro usnadnění práce se můžete připojit k virtuálnímu počítači pomocí prostředí JIT přímo ze stránky virtuálního počítače v Security Center.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Konfigurace přístupu JIT na virtuálním počítači přes stránku virtuálního počítače Azure

Aby bylo možné v rámci virtuálních počítačů snadno naprogramovat přístup za běhu, můžete nastavit virtuální počítač tak, aby umožňoval pouze přístup za běhu přímo z virtuálního počítače.

1. V [Azure Portal](https://ms.portal.azure.com)vyhledejte a vyberte **virtuální počítače**. 
2. Vyberte virtuální počítač, který chcete omezit na přístup za běhu.
3. V nabídce vyberte **Konfigurace**.
4. V části **přístup za běhu**vyberte **Povolit**za běhu. 

To umožňuje použít pro virtuální počítač přístup za běhu pomocí následujících nastavení:

- Servery Windows:
    - Port RDP 3389
    - Tři hodiny z maximálního povoleného přístupu
    - Povolené zdrojové IP adresy jsou nastavené na any.
- Servery Linux:
    - Port SSH 22
    - Tři hodiny z maximálního povoleného přístupu
    - Povolené zdrojové IP adresy jsou nastavené na any.
     
Pokud má virtuální počítač už povolený just-in-time, budete při přechodu na jeho stránku konfigurace moct zjistit, jestli je povolený za běhu, a můžete použít odkaz k otevření zásady v Azure Security Center k zobrazení a změně nastavení.

![Konfigurace JIT ve virtuálním počítači](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Vyžádat přístup JIT k virtuálnímu počítači prostřednictvím stránky virtuálního počítače Azure

Když se v Azure Portal pokusíte připojit k virtuálnímu počítači, Azure zkontroluje, jestli máte na daném virtuálním počítači nakonfigurovanou zásadu přístupu za běhu.

- Pokud máte na virtuálním počítači nakonfigurovanou zásadu JIT, můžete kliknout na **požádat o přístup** a udělit přístup v souladu se sadou zásad JIT nastavenou pro virtuální počítač. 

  >![požadavek JIT](./media/security-center-just-in-time/jit-request.png)

  Je požadován přístup s následujícími výchozími parametry:

  - **zdrojová IP adresa**: Any (*) (nedá se změnit)
  - **časový rozsah**: tři hodiny (nelze změnit) <!--Isn't this set in the policy-->
  - **číslo portu** Port RDP 3389 pro Windows/port 22 pro Linux (lze změnit)

    > [!NOTE]
    > Po schválení žádosti o virtuální počítač chráněný serverem Azure Firewall Security Center poskytne uživateli správné podrobnosti o připojení (mapování portů z tabulky DNAT), které se použijí pro připojení k virtuálnímu počítači.

- Pokud na virtuálním počítači není kompilátor JIT nakonfigurovaný, zobrazí se vám výzva ke konfiguraci zásad JIT.

  ![příkazový řádek JIT](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>Programové nakonfigurování zásad JIT na virtuálním počítači<a name="jit-program"></a>

Pomocí rozhraní REST API a prostředí PowerShell můžete nastavit a použít za běhu.

### <a name="jit-vm-access-via-rest-apis"></a>Přístup k virtuálnímu počítači JIT přes rozhraní REST API

Funkce přístupu k VIRTUÁLNÍm počítačům za běhu se dá použít přes rozhraní Azure Security Center API. Pomocí tohoto rozhraní API můžete získat informace o konfigurovaných virtuálních počítačích, přidat nové, vyžadovat přístup k virtuálnímu počítači a další. Další informace o REST API pro dobu běhu najdete v tématu [zásady přístupu k síti JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).

### <a name="jit-vm-access-via-powershell"></a>Přístup k virtuálnímu počítači JIT prostřednictvím PowerShellu

Pokud chcete použít řešení přístupu k virtuálnímu počítači podle potřeby přes PowerShell, použijte oficiální rutiny Azure Security Center PowerShellu a konkrétně `Set-AzJitNetworkAccessPolicy` .

Následující příklad nastaví zásady přístupu k VIRTUÁLNÍm počítačům za běhu na konkrétní virtuální počítač a nastaví následující:

1.    Zavřete porty 22 a 3389.

2.    Nastavte maximální časový interval pro každé 3 hodiny, aby bylo možné je otevřít na základě schválené žádosti.
3.    Umožňuje uživateli, který žádá o přístup k řízení zdrojových IP adres, a umožňuje uživateli vytvořit úspěšnou relaci v rámci schválené žádosti o přístup za běhu.

K tomu použijte následující příkaz v PowerShellu:

1.    Přiřaďte proměnnou, která obsahuje zásady přístupu k VIRTUÁLNÍm počítačům za běhu pro virtuální počítač:

        $JitPolicy = (@ {ID = "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";   porty = (@ {number = 22;        protokol = " \* ";        allowedSourceAddressPrefix = @ (" \* ");        maxRequestAccessDuration = "PT3H"}, @ {Number = 3389;        protokol = " \* ";        allowedSourceAddressPrefix = @ (" \* ");        maxRequestAccessDuration = "PT3H"})

2.    Do pole vložte zásady přístupu k virtuálnímu počítači za běhu podle potřeby:
    
        $JitPolicyArr = @ ($JitPolicy)

3.    Nakonfigurujte zásady přístupu k VIRTUÁLNÍm počítačům za běhu na vybraném virtuálním počítači:
    
        Set-AzJitNetworkAccessPolicy-Kind "základní" umístění "-Location" default "-ResourceGroupName" RESOURCENAME "-VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Vyžádat přístup k virtuálnímu počítači přes PowerShell

V následujícím příkladu uvidíte požadavek na přístup k virtuálnímu počítači za běhu na konkrétní virtuální počítač, ve kterém se vyžaduje otevření portu 22 pro konkrétní IP adresu a po určitou dobu:

Spusťte následující příkaz v PowerShellu:
1.    Konfigurovat vlastnosti přístupu k žádosti virtuálních počítačů

        $JitPolicyVm 1 = (@ {ID = "/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";   porty = (@ {number = 22;      endTimeUtc = "2018-09-17T17:00:00.3658798 Z";      allowedSourceAddressPrefix = @ ("IPV4ADDRESS")})
2.    Vložte parametry žádosti o přístup k virtuálnímu počítači do pole:

        $JitPolicyArr = @ ($JitPolicyVm 1)
3.    Poslat žádost o přístup (použijte ID prostředku, které jste získali v kroku 1)

        Start-AzJitNetworkAccessPolicy-ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default"-VirtualMachine $JitPolicyArr

Další informace najdete v [dokumentaci k rutinám PowerShellu](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Automatické čištění redundantních pravidel JIT 

Vždy, když aktualizujete zásadu JIT, nástroj pro vyčištění se automaticky spustí, aby zkontroloval platnost celého RuleSet. Nástroj vyhledá neshody mezi pravidly v zásadách a pravidly v NSG. Pokud nástroj pro vyčištění najde neshodu, určí příčinu a v případě, že je to bezpečné, odebere předdefinovaná pravidla, která ještě nejsou potřeba. Čisticí nikdy neodstraní pravidla, která jste vytvořili.

Příklady scénářů, kdy může čisticí modul odebrat předdefinované pravidlo:

- Pokud existují dvě pravidla se stejnými definicemi a jedna má vyšší prioritu než druhá (tzn. pravidlo nižší priority nebude nikdy použito)
- Když popis pravidla obsahuje název virtuálního počítače, který se neshoduje s cílovou IP adresou v pravidle. 


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak přístup k virtuálnímu počítači za běhu v Security Center pomáhá řídit přístup k virtuálním počítačům Azure.

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

- Modul Microsoft Learn [chrání vaše servery a virtuální počítače před útoky hrubou silou a malwarem pomocí Azure Security Center](https://docs.microsoft.com/learn/modules/secure-vms-with-azure-security-center/)
- [Nastavení zásad zabezpečení](tutorial-security-policy.md) – Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Správa doporučení zabezpečení](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
- [Sledování stavu zabezpečení](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
