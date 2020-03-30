---
title: Přístup k virtuálnímu počítači za chvíli v Azure Security Center | Dokumenty společnosti Microsoft
description: Tento dokument ukazuje, jak přístup k virtuálním počítačům v Azure Pomáhá řídit přístup k virtuálním počítačům Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 4b2b388fb736997010a6cbbdf93b23b77c7ef3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603968"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Zabezpečení portů pro správu pomocí přístupu just-in-time

Pokud jste na standardní cenové úrovni Security Center (viz [ceny),](/azure/security-center/security-center-pricing)můžete uzamknout příchozí provoz na virtuální počítače Azure s přístupem virtuálního počítače (VM) just-in-time (JIT). To snižuje vystavení útokům a zároveň poskytuje snadný přístup k připojení k virtuálním mům v případě potřeby.

> [!NOTE]
> Služba Security Center just-in-time přístup k virtuálním počítačům aktuálně podporuje jenom virtuální počítače nasazené prostřednictvím Správce prostředků Azure. Další informace o klasických modelech nasazení a modelech nasazení Správce prostředků najdete v [tématu Azure Resource Manager vs. klasické nasazení](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Konfigurace JIT na virtuálním počítači

Zásady JIT lze nakonfigurovat na virtuálním počítači třemi způsoby:

- [Konfigurace přístupu JIT v Azure Security Center](#jit-asc)
- [Konfigurace přístupu JIT na stránce virtuálního počítače Azure](#jit-vm)
- [Programová konfigurace zásad JIT na virtuálním počítači](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Konfigurace JIT v Azure Security Center

Z Security Center můžete nakonfigurovat zásady JIT a požádat o přístup k virtuálnímu počítače pomocí zásad y JIT

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>Konfigurace přístupu JIT na virtuálním počítači v Centru zabezpečení<a name="jit-asc"></a>

1. Otevřete řídicí panel **Security Center**.

1. V levém podokně vyberte **přístup k virtuálním ms just-in-time**.

    ![Dlaždice přístupu k virtuálním virtuálním můtím za včase](./media/security-center-just-in-time/just-in-time.png)

    Otevře **se okno přístupu k virtuálním počítačům za chvíli** a zobrazí informace o stavu virtuálních počítačů:

    - **Nakonfigurované** – virtuální počítače, které byly nakonfigurované tak, aby podporovaly přístup k virtuálním počítačem just-in-time. Prezentovaná data jsou za poslední týden a zahrnují pro každý virtuální ms počet schválených požadavků, datum a čas posledního přístupu a posledního uživatele.
    - **Doporučené** – virtuální počítače, které podporují přístup k virtuálním virtuálním počítačem just-in-time, ale nebyly nakonfigurované. Doporučujeme povolit řízení přístupu k virtuálním virtuálním můcích za včase pro tyto virtuální aplikace.
    - **Žádné doporučení** – Mezi důvody, proč virtuální počítač nemusí být doporučený, patří:
      - Chybějící nsg - řešení just-in-time vyžaduje nsg být na místě.
      - Klasický virtuální počítač – přístup k virtuálním počítači centra zabezpečení just-in-time, který momentálně podporuje jenom virtuální počítače nasazené prostřednictvím Správce prostředků Azure. Klasické nasazení není podporováno řešením just-in-time. 
      - Ostatní – virtuální ho virtuálního počítačů je v této kategorii, pokud je řešení just-in-time vypnuté v zásadách zabezpečení předplatného nebo skupiny prostředků, nebo pokud virtuální hod chybí veřejná IP adresa a nemá skupinu zabezpečení na místě.

1. Vyberte kartu **Doporučené.**

1. V části **VIRTUAL MACHINE**klikněte na virtuální počítače, které chcete povolit. Tím se vedle virtuálního virtuálního virtuálního virtuálního min zaškrtne tememe.

      ![Povolení přístupu za čase](./media/security-center-just-in-time/enable-just-in-time.png)

1. Klepněte **na položku Povolit JIT na virtuálních počítačích**. Otevře se podokno zobrazující výchozí porty doporučené Službou Zabezpečení Azure:
    - 22 - SSH
    - 3389 – PRV
    - 5985 - WinRM 
    - 5986 - WinRM
1. Volitelně můžete do seznamu přidat vlastní porty:

      1. Klikněte na **Přidat**. Otevře se okno **Přidat konfiguraci portu.**
      1. Pro každý port, který se rozhodnete konfigurovat, výchozí i vlastní, můžete přizpůsobit následující nastavení:
            - **Typ protokolu**- Protokol, který je povolen na tomto portu při schválení požadavku.
            - **Povolené zdrojové ADRESY IP**– Rozsahy IP adres, které jsou na tomto portu povoleny při schválení požadavku.
            - **Maximální doba požadavku**– maximální doba, během které lze otevřít konkrétní port.

     1. Klikněte na tlačítko **OK**.

1. Klikněte na **Uložit**.

> [!NOTE]
>Když je pro virtuální počítač povolen přístup k jit voblasti virtuálního počítače, Azure Security Center vytvoří pravidla "odepřít veškerý příchozí provoz" pro vybrané porty ve skupinách zabezpečení sítě přidružených a Azure Firewall s ním. Pokud byla pro vybrané porty vytvořena jiná pravidla, mají stávající pravidla přednost před novými pravidly "odepřít veškerý příchozí provoz". Pokud neexistují žádná existující pravidla pro vybrané porty, nové "odepřít všechny příchozí provoz" pravidla mají nejvyšší prioritu ve skupinách zabezpečení sítě a Azure Firewall.


## <a name="request-jit-access-via-security-center"></a>Požádat o přístup JIT prostřednictvím centra zabezpečení

Chcete-li požádat o přístup k virtuálnímu virtuálnímu serveru prostřednictvím Centra zabezpečení:

1. V **části Přístup k virtuálním montovně just-in-time**vyberte kartu **Nakonfigurovaný.**

1. V části **Virtuální počítač**klikněte na virtuální počítače, pro které chcete požádat o přístup. Tím se vedle virtuálního virtuálního virtuálního virtuálního míse zaškrtnete.

    - Ikona ve sloupci **Podrobnosti o připojení** označuje, zda je jit povolen v souboru zabezpečení sítě nebo FW. Pokud je povolena v obou, zobrazí se pouze ikona brány firewall.

    - Sloupec **Podrobnosti připojení** obsahuje informace potřebné k připojení virtuálního uživatele a jeho otevřených portů.

      ![Vyžádání přístupu za běhu](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Klepněte na **tlačítko Požádat o přístup**. Otevře se okno **Požádat o přístup.**

      ![Podrobnosti o JIT](./media/security-center-just-in-time/just-in-time-details.png)

1. V části **Požádat o přístup**nakonfigurujte pro každý virtuální počítač porty, které chcete otevřít, a zdrojové ADRESY IP, na kterých je port otevřen, a časové okno, pro které bude port otevřen. Bude možné pouze požádat o přístup k portům, které jsou nakonfigurovány v zásadách just-in-time. Každý port má maximální povolený čas odvozený od zásady just-in-time.

1. Klepněte na tlačítko **Otevřít porty**.

> [!NOTE]
> Pokud uživatel, který požaduje přístup, je za proxy serverem, nemusí fungovat možnost **Moje IP** adresa. Možná budete muset definovat úplný rozsah IP adres organizace.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Úprava zásad přístupu JIT prostřednictvím Centra zabezpečení

Můžete změnit stávající zásady virtuálního počítače just-in-time přidáním a konfigurací nového portu pro ochranu pro tento virtuální počítač nebo změnou jakéhokoli jiného nastavení souvisejícího s již chráněným portem.

Pokud chcete upravit existující zásady virtuálního virtuálního aplikace za čase:

1. Na **kartě Configured** v části **Virtuální počítače**vyberte virtuální počítač, ke kterému chcete přidat port, kliknutím na tři tečky v řádku pro tento virtuální počítač. 

1. Vyberte **Upravit**.

1. V části **Konfigurace přístupu k virtuálním počítačům JIT**můžete buď upravit existující nastavení již chráněného portu, nebo přidat nový vlastní port. 
  ![jit vm přístup](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Auditovat aktivitu přístupu JIT v Centru zabezpečení

Přehled o aktivitách virtuálních montovek můžete získat pomocí vyhledávání protokolů. Zobrazení protokolů:

1. V **části Přístup k virtuálním montovně just-in-time**vyberte kartu **Nakonfigurovaný.**
2. V části **Virtuální počítači**vyberte virtuální počítač a zobrazte informace o tom, že kliknete na tři tečky v řádku pro tento virtuální počítač a z nabídky vyberte **Protokol aktivit.** Otevře se **protokol aktivit.**

   ![Vybrat protokol aktivit](./media/security-center-just-in-time/select-activity-log.png)

   **Protokol aktivit** poskytuje filtrované zobrazení předchozích operací pro tento virtuální virtuální ms spolu s časem, datem a odběrem.

Informace protokolu si můžete stáhnout tak, že vyberete **Kliknutím sem stáhnete všechny položky jako CSV**.

Upravte filtry a kliknutím na **Použít** vytvořte hledání a protokol.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>Konfigurace přístupu JIT ze stránky virtuálního počítače Azure<a name="jit-vm"></a>

Pro vaše pohodlí se můžete připojit k virtuálnímu počítači pomocí JIT přímo ze stránky virtuálního počítače v Centru zabezpečení.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Konfigurace přístupu JIT na virtuálním počítači prostřednictvím stránky virtuálního počítače Azure

Chcete-li usnadnit zavedení přístupu just-in-time mezi virtuálními počítači, můžete nastavit virtuální počítač tak, aby umožňoval pouze přístup za chvíli přímo z virtuálního počítače.

1. Na [webu Azure Portal](https://ms.portal.azure.com)vyhledejte a vyberte **virtuální počítače**. 
2. Vyberte virtuální počítač, který chcete omezit na přístup za chvíli.
3. V nabídce vyberte **Možnost Konfigurace**.
4. V části **Přístup za chvíli**vyberte **Povolit just-in-time**. 

To umožňuje přístup za čas pro virtuální počítače pomocí následujících nastavení:

- Servery windows:
    - Port RDP 3389
    - Maximální povolený přístup na tři hodiny
    - Povolené zdrojové IP adresy jsou nastaveny na libovolný
- Linuxové servery:
    - SSH port 22
    - Maximální povolený přístup na tři hodiny
    - Povolené zdrojové IP adresy jsou nastaveny na libovolný
     
Pokud virtuální počítač už má povoleno just-in-time povoleno, když přejdete na jeho konfigurační stránku, uvidíte, že je povoleno just-in-time a můžete použít odkaz k otevření zásad v Centru zabezpečení Azure k zobrazení a změně nastavení.

![jit config v vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Žádost o přístup JIT k virtuálnímu počítači prostřednictvím stránky virtuálního počítače Azure

Na webu Azure Portal při pokusu o připojení k virtuálnímu počítači Azure zkontroluje, jestli máte na konfigurované zásady přístupu za čase na tomto virtuálním počítači.

- Pokud máte zásady JIT nakonfigurované na virtuálním počítači, můžete kliknout **na požádat o přístup** k udělení přístupu v souladu s sadou zásad JIT pro virtuální počítač. 

  >![jit požadavek](./media/security-center-just-in-time/jit-request.png)

  Přístup je požadován s následujícími výchozími parametry:

  - **zdroj IP**: 'Any' (*) (nelze změnit)
  - **časový rozsah**: Tři hodiny (nelze změnit) <!--Isn't this set in the policy-->
  - **číslo portu** RDP port 3389 pro Windows / port 22 pro Linux (lze změnit)

    > [!NOTE]
    > Po schválení požadavku pro virtuální počítač chráněný bránou Azure Firewall poskytuje Security Center uživateli správné podrobnosti o připojení (mapování portů z tabulky DNAT), které se mají použít k připojení k virtuálnímu počítači.

- Pokud nemáte JIT nakonfigurován na virtuálním počítači, budete vyzváni ke konfiguraci zásady JIT na něm.

  ![jit výzva](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>Programová konfigurace zásad JIT na virtuálním počítači<a name="jit-program"></a>

Můžete nastavit a používat just-in-time přes REST API a přes PowerShell.

### <a name="jit-vm-access-via-rest-apis"></a>Přístup k virtuálnímu virtuálnímu virtuálnímu připojení JIT prostřednictvím rest API

Funkci přístupu k virtuálním počítači just-in-time lze použít prostřednictvím rozhraní AZURE Security Center API. Můžete získat informace o nakonfigurovaných virtuálních počítače, přidat nové, požádat o přístup k virtuálnímu počítače a další, prostřednictvím tohoto rozhraní API. Další informace o rozhraní REST API just-in-time najdete v [tématu Zásady přístupu k jit.](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)

### <a name="jit-vm-access-via-powershell"></a>Přístup k virtuálnímu virtuálnímu připojení JIT přes PowerShell

Pokud chcete použít řešení pro přístup k virtuálním počítačům za čase prostřednictvím PowerShellu, `Set-AzJitNetworkAccessPolicy`použijte oficiální rutiny Azure Security Center PowerShell a konkrétně .

Následující příklad nastaví zásady přístupu k virtuálním počítači za čase na konkrétním virtuálním počítači a nastaví následující:

1.  Zavřete porty 22 a 3389.

2.  Nastavte maximální dobu 3 hodiny pro každý, takže mohou být otevřeny na základě schválenéžádosti.
3.  Umožňuje uživateli, který požaduje přístup k řízení zdrojových IP adres, a umožňuje uživateli vytvořit úspěšnou relaci na základě schválené žádosti o přístup za včase.

Chcete-li to provést, spusťte v Prostředí PowerShell následující:

1.  Přiřaďte proměnnou, která obsahuje zásady přístupu k virtuálním virtuálním zařízením za čase:

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

2.  Vložte zásady přístupu virtuálního zařízení just-in-time k poli:
    
        $JitPolicyArr=@($JitPolicy)

3.  Konfigurace zásady přístupu k virtuálním počítači za včase na vybraném virtuálním počítači:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Vyžádání přístupu k virtuálnímu virtuálnímu serveru přes PowerShell

V následujícím příkladu uvidíte požadavek na přístup virtuálních počítačů za chvíli ke konkrétnímu virtuálnímu virtuálnímu serveru, ve kterém se požaduje otevření portu 22 pro konkrétní IP adresu a na určitou dobu:

V PowerShellu spusťte následující:
1.  Konfigurace vlastností přístupu k požadavku na virtuální počítače

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Vložte parametry požadavku na přístup virtuálního zařízení do pole:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Odeslat žádost o přístup (použijte ID prostředku, které jste dostali v kroku 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Další informace naleznete v [dokumentaci rutiny prostředí PowerShell](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Automatické vyčištění nadbytečných pravidel JIT 

Při každé aktualizaci zásad JIT se automaticky spustí nástroj pro vyčištění, který zkontroluje platnost celé sady pravidel. Nástroj hledá neshody mezi pravidly ve vaší zásady a pravidla v souboru zásad nsg. Pokud nástroj pro vyčištění najde neshodu, určuje příčinu a pokud je to bezpečné, odebere předdefinovaná pravidla, která už nejsou potřeba. Čistič nikdy neodstraní pravidla, která jste vytvořili.

Příklady scénářů, kdy může čistič odebrat předdefinované pravidlo:

- Pokud existují dvě pravidla se stejnými definicemi a jedno má vyšší prioritu než druhé (což znamená, že pravidlo s nižší prioritou se nikdy nepoužije)
- Když popis pravidla obsahuje název virtuálního počítačů, který neodpovídá cílové IP adrese v pravidle 


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak přístup k virtuálním počítačům v centru zabezpečení pomáhá řídit přístup k virtuálním počítačům Azure.

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

- [Nastavení zásad zabezpečení](tutorial-security-policy.md) – Zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Správa doporučení zabezpečení](security-center-recommendations.md) – Zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
- [Monitorování stavu zabezpečení](security-center-monitoring.md) – Přečtěte si, jak sledovat stav prostředků Azure.