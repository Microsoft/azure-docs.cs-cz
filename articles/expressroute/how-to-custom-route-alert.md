---
title: 'ExpressRoute: jak nakonfigurovat vlastní výstrahy pro inzerované trasy'
description: V tomto článku se dozvíte, jak pomocí Azure Automation a Logic Apps monitorovat počet tras inzerovaných z brány ExpressRoute do místních sítí, aby se předešlo tomu, že se zasáhne limit trasy 1000.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: 2291d1fa7f890296c59661060f5a823d8eb194ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654386"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Konfigurace vlastních upozornění pro monitorování inzerovaných tras

Tento článek vám pomůže s použitím Azure Automation a Logic Apps k nepřetržitému monitorování počtu tras inzerovaných z brány ExpressRoute do místních sítí. Monitorování může přispět k tomu, aby se zabránilo zvýšení limitu trasy 1000] (ExpressRoute-FAQ. MD # how-m:n----many------li----------ExpressRoute-Private-peering-on-----------

**Azure Automation** umožňuje automatizovat spouštění vlastního skriptu PowerShellu uloženého v *Runbooku*. Při použití konfigurace v tomto článku sada Runbook obsahuje skript PowerShellu, který se dotazuje na jednu nebo více ExpressRoute bran. Shromažďuje datovou sadu obsahující skupinu prostředků, název brány ExpressRoute a počet předpon sítě inzerovaných místně.

**Azure Logic Apps** naplánuje vlastní pracovní postup, který volá sadu Runbook Azure Automation. Spuštění Runbooku se provádí pomocí úlohy. Po spuštění shromažďování dat Azure Logic Apps pracovní postup tato data roztřídí a na základě kritérií shody v počtu předpon sítě nad nebo pod předdefinovaným prahem odesílá informace do cílové e-mailové adresy.

### <a name="workflow"></a><a name="workflow"></a>Pracovní postup

Nastavení vlastní výstrahy je založené na třech hlavních krocích:

1. Vytvořte účet Automation s účtem a oprávněními "spustit jako".

2. Vytvořte a nakonfigurujte Runbooky.

3. Vytvořte aplikaci logiky, která aktivuje účet Automation, a odešle e-mail s upozorněním, pokud je počet větší než prahová hodnota (například 160).

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* V nasazení máte alespoň jednu bránu ExpressRoute.

* Máte základní informace o [účtech Spustit jako](../automation/manage-runas-account.md) v Azure Automation.

* Jste obeznámeni s [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

* Jste obeznámeni s používáním Azure PowerShell. Pro shromáždění předpon sítě v bráně ExpressRoute se vyžaduje Azure PowerShell. Další informace o Azure PowerShell obecně najdete v [dokumentaci k Azure PowerShell](/powershell/azure/).

### <a name="notes-and-limitations"></a><a name="limitations"></a>Poznámky a omezení

* Vlastní výstraha popisovaná v tomto článku je doplněk pro dosažení lepšího provozu a řízení. Nejedná se o náhradu za nativní výstrahy v ExpressRoute.
* Shromažďování dat pro brány ExpressRoute běží na pozadí. Doba běhu může být delší, než se očekávalo. Aby nedošlo k zařazování do fronty úloh, je nutné správně nastavit opakování pracovního postupu.
* Nasazení pomocí skriptů nebo šablon ARM může nastat rychleji než vlastní aktivační událost alarmu. To může vést ke zvýšení počtu předpon sítě v bráně ExpressRoute nad limit 1000 tras.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Vytvoření a konfigurace účtů

Když v Azure Portal vytvoříte účet Automation, automaticky se vytvoří účet [Spustit jako](../automation/automation-security-overview.md#run-as-accounts) . Tento účet provede následující akce:

* Vytvoří aplikaci Azure Active Directory (Azure AD) s certifikátem podepsaným svým držitelem. Samotný účet Spustit jako má certifikát, který je potřeba obnovit ve výchozím nastavení každý rok.

* Vytvoří hlavní účet služby pro aplikaci ve službě Azure AD.

* Přiřadí roli přispěvatele (Azure RBAC) k používanému předplatnému Azure. Tato role spravuje Azure Resource Manager prostředky pomocí runbooků.

Aby bylo možné vytvořit účet Automation, potřebujete oprávnění a oprávnění. Informace najdete v tématu [oprávnění požadovaná k vytvoření účtu Automation](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account).

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. vytvoření účtu Automation

Vytvořte účet Automation s oprávněními spustit jako. Pokyny najdete v tématu [Vytvoření účtu Azure Automation](../automation/automation-quickstart-create-account.md).

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Přidat účet Automation" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. přiřaďte účet Spustit jako roli.

Ve výchozím nastavení je role **přispěvatele** přiřazena k instančnímu objektu, který používá váš účet **Spustit jako** . Můžete ponechat výchozí roli přiřazenou instančnímu objektu, nebo můžete omezit oprávnění přiřazením [předdefinované role](../role-based-access-control/built-in-roles.md) (například čtenář) nebo [vlastní role](../active-directory/roles/custom-create.md).

 Pomocí následujících kroků určete přiřazení role k instančnímu objektu, který používá váš účet Spustit jako:

1. Přejděte do svého účtu Automation. Přejděte na **Nastavení účtu** a pak vyberte **účty Spustit jako**.

2. Vyberte **role** pro zobrazení používané definice role.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Přiřazení role":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Vytváření a konfigurace runbooků

### <a name="1-install-modules"></a><a name="install-modules"></a>1. Instalace modulů

Aby bylo možné spouštět rutiny prostředí PowerShell v Azure Automation Runbooky, je třeba nainstalovat několik dalších Azure PowerShell AZ Modules. Moduly můžete nainstalovat pomocí následujících kroků:

1. Otevřete účet Azure Automation a přejděte do **modulů**.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="Přejít k modulům":::

2. Vyhledejte v galerii a importujte následující moduly: **AZ. Accounts**, **AZ. Network**, **AZ. Automation** a **AZ. Profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Hledání a import modulů" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. vytvoření Runbooku

1. Pokud chcete vytvořit PowerShellový Runbook, přejděte k účtu Automation. V části **Automatizace procesu** vyberte dlaždici **Runbooky** a pak vyberte **vytvořit Runbook**.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Vytvořte sadu Runbook.":::

2. Vyberte **vytvořit** a vytvořte sadu Runbook.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Vyberte vytvořit.":::

3. Vyberte nově vytvořenou sadu Runbook a pak vyberte **Upravit**.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Úprava runbooku":::

4. V části **Upravit** vložte powershellový skript. [Vzorový skript](#script) se dá upravit a použít k monitorování bran ExpressRoute v jedné nebo více skupinách prostředků.

   V ukázkovém skriptu si všimněte následujícího nastavení:

    * Pole **$rgList** obsahuje seznam skupin prostředků s branami ExpressRoute. Můžete přizpůsobit brány ExpressRoute založené na seznamu.
    * Proměnná **$thresholdNumRoutes** definuje prahovou hodnotu počtu předpon sítě inzerovaných z brány ExpressRoute do místních sítí.

#### <a name="example-script"></a><a name="script"></a>Ukázkový skript

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. Uložte a publikujte Runbook.

1. Vyberte **Uložit** a uložte tak koncept kopie Runbooku.
2. Vyberte **publikovat** a publikujte Runbook jako oficiální verzi Runbooku v účtu Automation.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Uložte a publikujte Runbook.":::

Při spuštění skriptu PowerShellu se shromáždí seznam hodnot:
 
* Skupina prostředků

* Název brány ExpressRoute

* IP adresa prvního partnerského uzlu protokolu BGP (peer1)

* IP adresa druhého partnerského uzlu protokolu BGP (peer2)

* Počet předpon sítě inzerovaných od brány ExpressRoute k prvnímu partnerskému uzlu protokolu BGP (peer1)

* Počet předpon sítě inzerovaných od brány ExpressRoute k druhému partnerskému uzlu protokolu BGP (peer2)

* Timestamp

* Stav klasifikovaný jako:

  * ' OK ', pokud je počet tras menší než prahová hodnota
  * ' ALERT ', pokud je počet tras nad prahovou hodnotou
  * ' WARNING ', pokud se liší počet předpon sítě inzerovaných dvou partnerských uzlů BGP

* Zpráva s upozorněním pro podrobný popis stavu (OK, VÝSTRAHa, upozornění)

Skript prostředí PowerShell převede shromážděné informace na výstup JSON. Sada Runbook pomocí rutiny PowerShellu [Write-Output](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)  jako výstupní Stream oznamuje informace klientovi.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. ověření Runbooku

Po vytvoření se Runbook musí ověřit. Vyberte **Start** a ověřte výstup a chyby pro různé streamy úloh.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Ověřit sadu Runbook" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Vytvoření a konfigurace aplikace logiky

Azure Logic Apps je Orchestrator všech procesů shromažďování a akcí. V následujících částech sestavíte pracovní postup pomocí aplikace logiky.

### <a name="workflow"></a>Pracovní postup

V tomto pracovním postupu vytvoříte aplikaci logiky, která pravidelně monitoruje brány ExpressRoute. Pokud se najdou nové položky, aplikace logiky za každou z nich odešle e-mail. Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Pracovní postup Logic Apps":::

### <a name="1-create-a-logic-app"></a>1. Vytvoření aplikace logiky

V **Návrháři aplikace logiky** vytvořte aplikaci logiky pomocí **prázdné šablony aplikace logiky** . Postup najdete v tématu [vytvoření Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app).

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Prázdná šablona":::

### <a name="2-add-a-trigger"></a>2. Přidání triggeru

Každá aplikace logiky se spouští triggerem. Trigger se aktivuje, když dojde ke konkrétní události, nebo při splnění konkrétní podmínky. Pokaždé, když se Trigger aktivuje, modul Azure Logic Apps vytvoří instanci aplikace logiky, která spustí a spustí váš pracovní postup.

K pravidelnému spuštění aplikace logiky, která je založena na předdefinovaném časovém plánu, přidejte předdefinované **opakování: plán** do pracovního postupu. Do vyhledávacího pole zadejte **Schedule**. Vyberte **triggery**. V seznamu triggery vyberte **plán opakování**.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Opakování: plán":::

V aktivační události plánu opakování můžete nastavit časové pásmo a opakování pro opakování tohoto pracovního postupu. Interval a frekvence společně definují plán pro trigger vaší aplikace logiky. K navázání přiměřené minimální četnosti opakování Vezměte v úvahu následující faktory:

* Dokončení skriptu PowerShellu v sadě Automation je čas. Modul Runtime závisí na počtu bran ExpressRoute, které se mají monitorovat. Příliš krátká frekvence opakování způsobí, že bude služba Řízení front úloh.

* PowerShellový skript se spouští jako úloha na pozadí. Nespouští se okamžitě. Spustí se po zpoždění proměnné.

* Příliš krátká frekvence opakování vygeneruje nepotřebné zatížení bran Azure ExpressRoute.

Na konci Konfigurace pracovního postupu můžete ověřit konzistenci četnosti opakování tak, že několikrát spustíte pracovní postup a pak ověříte výsledek v **historii spuštění**.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="Snímek obrazovky ukazuje interval opakování a hodnoty frekvence." lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. vytvoření úlohy

Aplikace logiky přistupuje k jiným aplikacím, službám a platformě i přes konektory. Dalším krokem v tomto pracovním postupu je výběr konektoru pro přístup k účtu Azure Automation, který jste definovali dříve.

1. V **Logic Apps Návrhář** pod **opakováním** vyberte **Nový krok**. V části **Zvolte akci** a vyhledávací pole vyberte **vše**.
2. Do vyhledávacího pole zadejte **Azure Automation** a vyhledejte. Vyberte **vytvořit úlohu**. **Úloha vytvoření** bude sloužit k vyvolání sady Automation Runbook, který byl vytvořen dříve.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="Vytvoření úlohy":::

3. Přihlaste se pomocí instančního objektu. Můžete použít existující instanční objekt, nebo můžete vytvořit nový. Pokud chcete vytvořit nový instanční objekt, přečtěte si téma [jak použít portál k vytvoření instančního objektu služby Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md). Vyberte **připojit s objektem služby**.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="Snímek obrazovky, který zobrazuje oddíl opakování s zvýrazněnou akcí připojit s instančním objektem":::

4. Zadejte **název připojení**, přidejte **ID klienta** (ID aplikace), **tajný klíč klienta** a **ID vašeho tenanta**. Potom vyberte **Vytvořit**.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Připojit s instančním objektem":::

5. Na stránce **vytvořit úlohu** by měl instanční objekt mít roli Čtenář v této **skupině prostředků** , která je hostitelem účtu Automation, a "operátor úlohy služby Automation" na **účtu Automation**. Dále ověřte, že jste přidali **název sady Runbook** jako nový parametr.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Snímek obrazovky ukazuje, jak vytvořit hodnoty úloh v opakování, kde můžete ověřit název Runbooku." lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. Získejte výstup úlohy.

1. Vyberte **Nový krok**. Vyhledejte "Azure Automation". V seznamu **Akce** vyberte **získat výstup úlohy**.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="Získání výstupu úlohy":::

2. Na stránce **načíst výstup úlohy** zadejte požadované informace pro přístup k účtu Automation. Vyberte **předplatné, skupinu prostředků** a **účet Automation** , který chcete použít. Klikněte do pole **ID úlohy** . Až se zobrazí seznam **dynamického obsahu** , vyberte **ID úlohy**.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="ID úlohy" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. Analyzujte JSON.

Informace obsažené ve výstupu z akce Azure Automation vytvořit úlohu úlohy (předchozí kroky) generují objekt JSON. Logic Apps **Analýza JSON** je vestavěná akce pro vytváření uživatelsky přívětivých tokenů z vlastností a jejich hodnot v obsahu JSON. Tyto vlastnosti pak můžete použít v pracovním postupu.

1. Přidat akci. V části **získat výstup úlohy >akci** vyberte **Nový krok**.
2. Do vyhledávacího pole **zvolit akci** zadejte "analyzovat JSON" a vyhledejte konektory, které tuto akci nabízejí. V seznamu **Akce** vyberte akci **analyzovat JSON** pro datové operace, které chcete použít.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="Parsování formátu JSON":::

3. Klikněte do pole **obsah** . Když se zobrazí seznam dynamický obsah, vyberte **obsah**.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Snímek obrazovky s vybraným obsahem zobrazuje dialogové okno analýzy formátu JSON." lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. Analýza JSON vyžaduje schéma. Schéma se dá vygenerovat pomocí výstupu Runbooku služby Automation. Otevřete novou relaci webového prohlížeče, spusťte Runbook služby Automation a natáhněte výstup. Vraťte se do akce **Logic Apps analyzovat operace dat JSON** . V dolní části stránky vyberte **použít ukázkovou datovou část k vygenerování schématu**.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Použít ukázkovou datovou část k vygenerování schématu":::

5. V poli **Zadejte nebo vložte ukázkovou datovou část JSON** vložte výstup Runbooku služby Automation a vyberte **Hotovo**.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Vložit ukázkovou datovou část" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. Schéma je automaticky vygenerováno analýzou datové části vstupního formátu JSON.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Generovat schéma" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. definování a inicializace proměnné

V tomto kroku pracovního postupu vytvoříme podmínku pro odeslání alarmu e-mailem. V případě flexibilního vlastního formátování zprávy těla e-mailu je do pracovního postupu představena pomocná proměnná.

1. V **akci načíst výstup úlohy** vyberte **Nový krok**. Ve vyhledávacím poli vyhledejte a vyberte **proměnné**.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="Snímek obrazovky se zobrazí dialogové okno zvolit akci s proměnnou v poli Hledat a vybrané proměnné.":::

2. V seznamu **Akce** vyberte akci **inicializovat proměnnou** .

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Inicializovat proměnné":::

3. Zadejte název proměnné. Jako **typ** vyberte **řetězec**. **Hodnota** proměnné bude přiřazena později v pracovním postupu.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="Snímek obrazovky znázorňující analýzu JSON spojenou s proměnnou Initialize, kde můžete zadat název, typ a hodnotu." lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. vytvoření akce for each

Po analýze formátu JSON akce **operace analýzy dat JSON** uloží obsah do výstupního *textu* . Chcete-li zpracovat výstup, můžete vytvořit smyčku "pro každou", která opakuje jednu nebo více akcí u každé položky v poli.

1. V části **inicializovat proměnnou** vyberte **přidat akci**. Do vyhledávacího pole zadejte jako filtr "for each".

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Snímek obrazovky se zobrazí dialogové okno zvolit akci pro každý z vyhledávacího pole a vybraného ovládacího prvku.":::

2. V seznamu **Akce** vyberte akci **pro každý ovládací prvek**.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="Pro každý ovládací prvek":::

3. Klikněte na textové pole **vybrat výstup z předchozích kroků** . Po zobrazení seznamu **dynamický obsah** vyberte **text**, který je výstupem z analyzovaného formátu JSON.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="Snímek obrazovky se zobrazí inicializovaná proměnná přidružená k pro každou, která obsahuje textové pole vybrat výstup z předchozích kroků.":::

4. Pro každý prvek těla JSON chceme nastavit podmínku. Ve skupině akcí vyberte **ovládací prvek**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Řízení":::

5. V seznamu **Akce** vyberte možnost **řízení podmíněného řízení**. Condition-Control je struktura ovládacího prvku porovnává data v pracovním postupu s konkrétními hodnotami nebo poli. Pak můžete zadat různé akce, které se spustí na základě toho, jestli data splňují podmínky.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Řízení podmínek":::

6. V akci kořene **podmínky** změňte operaci logiky na **nebo**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Nebo" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. Ověřte hodnotu pro počet předpon sítě, které brána ExpressRoute Gateway inzeruje ke dvěma partnerským uzlům protokolu BGP. Počet tras je k dispozici v části "numRoutePeer1" a "numRoutePeer2" v **dynamickém obsahu**. Do pole hodnota zadejte hodnotu pro **numRoutePeer1**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. Pokud chcete do podmínky přidat další řádek, vyberte **Přidat-> přidat řádek**. Ve druhém poli z **dynamického obsahu** vyberte možnost **numRoutePeer2**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. Podmínka Logic je pravdivá, pokud jedna ze dvou dynamických proměnných, numRoute1 nebo numRoute2, je větší než prahová hodnota. V tomto příkladu je prahová hodnota opravena na 800 (80% maximální hodnoty 1000 trasy). Prahovou hodnotu můžete změnit tak, aby vyhovovala vašim požadavkům. V případě konzistence by měla být hodnota stejná jako hodnota použitá ve skriptu Runbooku PowerShellu.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Logická podmínka":::

10. V části Pokud je nastavené na **true**, naformátujte a vytvořte akce pro odeslání výstrahy e-mailem. V * * zvolte akci, vyhledejte a vyberte **proměnné**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="Při hodnotě true":::

11. V proměnné vyberte **přidat akci**. V seznamu **Akce** vyberte možnost **nastavit proměnnou**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="Snímek obrazovky s oddílem Variables a vybraným tabulátorem Actions a zvýrazněnou možností nastavit proměnnou":::

12. V poli **název** vyberte proměnnou s názvem **EmailBody** , kterou jste vytvořili dříve. Do pole **hodnota** vložte skript HTML vyžadovaný k naformátování e-mailu s upozorněním. **Dynamický obsah** použijte k zahrnutí hodnot těla JSON. Po nakonfigurování těchto nastavení je výsledkem, že proměnná **emailBody** obsahuje všechny informace týkající se výstrahy, ve formátu HTML.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Nastavit proměnnou":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. Přidání e-mailového konektoru

Logic Apps poskytuje spoustu e-mailových konektorů. V tomto příkladu přidáme konektor Outlooku, který pošle upozornění e-mailem. V části **nastavit proměnnou** vyberte **přidat akci**. V části **zvolit akci** zadejte do vyhledávacího pole "Odeslat e-mail".

1. Vyberte **Office 365 Outlook**.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="Poslat e-mail":::

2. V seznamu **Akce** vyberte **Odeslat e-mail (v2)**.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="Poslat e-mail (v2)":::

3. Přihlaste se a vytvořte připojení k Office 365 Outlooku.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Přihlásit":::

4. V poli **tělo** klikněte na **Přidat dynamický obsah**. Z panelu dynamického obsahu přidejte proměnnou **emailBody**. Vyplňte pole **Předmět** **a pole** .

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Text":::

5. Akce **Odeslat e-mail (v2)** dokončí nastavení pracovního postupu.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="Poslat e-mail v2" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. ověření pracovního postupu

Posledním krokem je ověření pracovního postupu. V **Logic Apps přehled** vyberte **Spustit Trigger**. Vyberte **opakování**. Pracovní postup se dá monitorovat a ověřit v **historii spuštění**.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Aktivační událost spuštění":::

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak tento pracovní postup přizpůsobit, najdete v tématu [Azure Logic Apps](../logic-apps/logic-apps-overview.md).
