---
title: Migrace Azure Cloud Services (Classic) do Azure Cloud Services (Rozšířená podpora)
description: Přehled migrace z Cloud Services (Classic) do cloudové služby (Rozšířená podpora)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 96315899f80d0bd02ac3d2108b7cd76876025218
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286871"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Migrace Azure Cloud Services (Classic) do Azure Cloud Services (Rozšířená podpora)

Tento článek poskytuje přehled nástroje pro migraci podporovaného platformou a jeho použití k migraci [azure Cloud Services (Classic)](../cloud-services/cloud-services-choose-me.md) do [Azure Cloud Services (Rozšířená podpora)](overview.md).

Nástroj pro migraci využívá stejná rozhraní API a má stejné prostředí jako [migrace virtuálního počítače (Classic)](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview). 

> [!IMPORTANT]
> Migrace z Cloud Services (Classic) na Cloud Services (Rozšířená podpora) pomocí nástroje pro migraci je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud potřebujete pomoc s migrací, přečtěte si následující zdroje informací: 

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html): Podpora Microsoftu a komunity pro migraci.
- [Podpora migrace do Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D): tým vyhrazené podpory pro technickou pomoc při migraci. Zákazníci bez technické podpory můžou využít [bezplatné možnosti podpory](https://aka.ms/cs-migration-errors) , které jsou specifické pro tuto migraci.
- Pokud vaše společnost nebo organizace spolupracuje s Microsoftem nebo spolupracuje se zástupci Microsoftu, jako jsou architekti cloudových řešení nebo techničtí manažeři, kontaktujte je pro další prostředky pro migraci.
- [Tento průzkum](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u) dokončete, pokud chcete poskytnout zpětnou vazbu nebo vyvolat problémy týmu produktu Cloud Services (rozšířené podpory). 

## <a name="migration-benefits"></a>Výhody migrace
Migrace podporovaná platformou poskytuje následující klíčové výhody:
- Migrace je plně orchestrovaná platformou a přesouvá celé nasazení a přidružené prostředky do Azure Resource Manager.
- Žádná migrace výpadků.
- Snadná a rychlá migrace v porovnání s jinými migračními cestami díky minimalizaci ručních úloh. 
- V rámci migrace uchová Cloud Services IP adresu a název DNS. 

Další výhody a důvody, proč byste měli migrovat, najdete v tématu [Cloud Services (Rozšířená podpora)](overview.md) a [Azure Resource Manager](../azure-resource-manager/management/overview.md). 

## <a name="setup-access-for-migration"></a>Nastavit přístup pro migraci

K provedení této migrace musíte být přidáni jako spolusprávce pro předplatné a zaregistrujte potřebné poskytovatele. 

1. Přihlaste se k webu Azure Portal.
3. V nabídce centra vyberte předplatné. Pokud ho nevidíte, vyberte všechny služby.
3. Vyhledejte odpovídající položku pro odběr a potom se podívejte do pole Moje ROLE. Pro spolusprávce by měla být hodnota správce účtu. Pokud nemůžete přidat spolusprávce, obraťte se na správce služby nebo spolupracujícího správce, aby vás přidal.

4. Registrace předplatného pro obor názvů Microsoft. ClassicInfrastructureMigrate pomocí [portálu](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShellu](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) nebo rozhraní příkazového [řádku](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Registrace předplatného pro funkci Cloud Services Migration Preview pomocí [portálu](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShellu](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) nebo rozhraní příkazového [řádku](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. Ověřte stav vaší registrace. Dokončení registrace může trvat několik minut. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Jak se migrace pro Cloud Services (Classic) liší od Virtual Machines (Classic)?
Azure Service Manager podporuje dva různé výpočetní produkty, [azure Virtual Machines (Classic)](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) a [Azure Cloud Services (Classic)](../cloud-services/cloud-services-choose-me.md) nebo webové a pracovní role. Tyto dva produkty se liší v závislosti na typu nasazení, který leží v rámci cloudové služby. Azure Cloud Services (Classic) používá cloudovou službu obsahující nasazení s rolemi web/Worker. Azure Virtual Machines (Classic) používá cloudovou službu obsahující nasazení s virtuálními počítači s IaaS.

Seznam podporovaných scénářů se liší od Cloud Services (Classic) a Virtual Machines (Classic) z důvodu rozdílů v typech nasazení.

## <a name="migration-steps"></a>Kroky migrace
 
Zákazníci můžou migrovat nasazení Cloud Services (Classic) pomocí stejných čtyř operací, které se použily k migraci Virtual Machines (Classic). 

1. **Ověřit migraci** – ověří, že by migrace nebránila běžným nepodporovaným scénářům.
2. **Příprava migrace** – duplikuje metadata prostředků v Azure Resource Manager. Všechny prostředky jsou uzamčené pro operace vytvoření, aktualizace nebo odstranění, aby se zajistila synchronizace metadat prostředků napříč Azure Správce serveru a Azure Resource Manager. Všechny operace čtení budou fungovat s použitím rozhraní API pro Cloud Services (Classic) i Cloud Services (Rozšířená podpora).
3. **Přerušení migrace** – Odebere metadata prostředků z Azure Resource Manager. Odemkne všechny prostředky pro operace vytvoření/aktualizace/odstranění.
4. **Potvrzení migrace** – Odebere metadata prostředků z Azure Service Manager. Odemkne prostředek pro operace vytvoření/aktualizace/odstranění. Přerušení již není povoleno po pokusů o potvrzení.

>[!NOTE]
> Příprava, přerušení a potvrzení jsou idempotentní, a proto by při selhání mělo problém vyřešit opakování.

:::image type="content" source="media/in-place-migration-1.png" alt-text="Obrázek znázorňuje diagram kroků přidružených k migraci.":::

Další informace najdete v tématu [Přehled migrace prostředků IaaS podporovaných platformou z modelu Classic na Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Podporované prostředky a funkce, které jsou k dispozici pro migraci přidružené k Cloud Services (Classic)
-   Účty úložiště
-   Virtuální sítě
-   Network Security Groups (Skupiny zabezpečení sítě)
-   Rezervované veřejné IP adresy
-   Access Controlové seznamy koncových bodů
-   Trasy definované uživatelem
-   Interní nástroj pro vyrovnávání zatížení
-   Migrace certifikátů do trezoru klíčů
-   Moduly plug-in a rozšíření (založené na XML a JSON)
-   Při spuštění/zastavení úloh zastavení
-   Nasazení s akcelerovanými síťovými službami
-   Nasazení pomocí jedné nebo více rolí
-   Základní nástroj pro vyrovnávání zatížení
-   Vstup, vstup instance, interní koncové body
-   Dynamické veřejné IP adresy
-   Název DNS 
-   Pravidla síťového provozu
-   Virtuální síť HyperNET

## <a name="supported-configurations--migration-scenarios"></a>Podporované scénáře konfigurace/migrace
Toto jsou hlavní scénáře zahrnující kombinace prostředků, funkcí a Cloud Services. Tento seznam není vyčerpávající.

| Služba | Konfigurace | Komentáře | 
|---|---|---|
| [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) | Virtuální sítě, které obsahují Azure Active Directory Domain Services. | Podporuje se virtuální síť, která obsahuje nasazení cloudové služby i službu Azure AD Domain Services. Zákazník nejdřív potřebuje samostatně migrovat službu Azure AD Domain Services a pak migrovat virtuální síť vlevo jenom s nasazením cloudové služby. |
| Cloudová služba | Cloudová služba s nasazením pouze v jednom slotu. | Je možné migrovat Cloud Services, které obsahují nasazení v rámci výrobního slotu nebo přípravného slotu |
| Cloudová služba | Nasazení není ve veřejně viditelné virtuální síti (výchozí nasazení virtuální sítě). | Cloudová služba může být ve veřejně viditelné virtuální síti, ve skryté virtuální síti nebo v žádné virtuální síti.  Migrace podporuje Cloud Services ve skryté virtuální síti a veřejně viditelných virtuálních sítích. Zákazník může použít ověřovací rozhraní API k určení, jestli je nasazení uvnitř výchozí virtuální sítě, nebo ne, a určí, jestli se dá migrovat. |
|Cloudová služba | Rozšíření XML (BGInfo, ladicí program sady Visual Studio, Nasazení webu a vzdálené ladění). | Všechna rozšíření XML jsou podporovaná pro migraci. 
| Virtual Network | Virtuální síť obsahující více Cloud Services. | Služba Virtual Network obsahuje více cloudových služeb, které jsou podporovány pro migraci. Virtuální síť a všechny Cloud Services v ní budou migrovány společně s Azure Resource Manager. |
| Virtual Network | Migrace virtuálních sítí vytvořených prostřednictvím portálu (vyžaduje, abyste v souboru. cscfg používali položku název skupiny Resource-Group-Name VNet-Name)  | V rámci migrace se název virtuální sítě v cscfg změní tak, aby používal Azure Resource Manager ID virtuální sítě. (předplatné/předplatné-ID/prostředek-skupina/prostředek-skupina-název/prostředek/VNet-Name) <br><br>Chcete-li spravovat nasazení po migraci, aktualizujte místní kopii souboru. cscfg, aby bylo možné začít používat Azure Resource Manager ID místo názvu virtuální sítě. <br><br>Soubor. cscfg, který používá staré schéma pojmenování, neprojde ověřením. 
| Virtual Network | Migrace nasazení s rolemi v jiné podsíti. | Pro migraci se podporuje cloudová služba s různými rolemi v různých podsítích. |
    

## <a name="resources-and-features-not-available-for-migration"></a>Prostředky a funkce, které nejsou k dispozici pro migraci
Toto jsou hlavní scénáře zahrnující kombinace prostředků, funkcí a Cloud Services. Tento seznam není vyčerpávající. 

| Prostředek | Další kroky/práce | 
|---|---|
| Pravidla automatického škálování | Migrace probíhá přes ale pravidla se vynechává. [Znovu vytvořte pravidla](https://docs.microsoft.com/azure/cloud-services-extended-support/configure-scaling) po migraci na Cloud Services (Rozšířená podpora). | 
| Výstrahy | Migrace probíhá, ale výstrahy se zahozeny. [Znovu vytvořte pravidla](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-alerts) po migraci na Cloud Services (Rozšířená podpora). | 
| VPN Gateway | Před zahájením migrace odeberte VPN Gateway a po dokončení migrace znovu vytvořte VPN Gateway. | 
| Brána Express Route (ve stejném předplatném jako Virtual Network) | Před zahájením migrace odeberte bránu Express Route a po dokončení migrace pak znovu vytvořte bránu. | 
| Kvóta  | Kvóta není migrována. [Vyžádejte si novou kvótu](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-resource-quota#solution) pro Azure Resource Manager před migrací, aby bylo ověření úspěšné. | 
| Skupiny vztahů | Nepodporováno Před migrací odeberte všechny skupiny vztahů.  | 
| Virtuální sítě s využitím [partnerského vztahu virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)| Před migrací virtuální sítě, která je v partnerském vztahu k jiné virtuální síti, odstraňte partnerský vztah, migrujte virtuální síť do Správce prostředků a vytvořte partnerský vztah znovu. To může v závislosti na architektuře způsobit výpadky. | 
| Virtuální sítě obsahující App Service prostředí | Nepodporováno | 
| Virtuální sítě obsahující služby HDInsight | Nepodporováno 
| Virtuální sítě obsahující nasazení služby Azure API Management | Nepodporováno <br><br> Chcete-li migrovat virtuální síť, změňte virtuální síť nasazení API Management. Nejedná se o operaci výpadku. | 
| Klasické okruhy Express Route | Nepodporováno <br><br>Tyto okruhy se musí před zahájením migrace PaaS migrovat na Azure Resource Manager. Další informace najdete v tématu [Přesun okruhů ExpressRoute z modelu nasazení Classic do modelu nasazení Správce prostředků](../expressroute/expressroute-howto-move-arm.md). |  
| Řízení přístupu na základě rolí | Po migraci je potřeba po migraci aktualizovat identifikátor URI, ze kterého se prostředek změní, `Microsoft.ClassicCompute` na `Microsoft.Compute` zásady RBAC. | 
| Application Gateway | Nepodporuje se. <br><br> Než začnete s migrací, odeberte Application Gateway a pak Application Gateway po dokončení migrace do Azure Resource Manager znovu vytvořte. | 

## <a name="unsupported-configurations--migration-scenarios"></a>Nepodporované konfigurace/scénáře migrace

| Konfigurace/scénář  | Další kroky/práce | 
|---|---|
| Migrace některých starších nasazení mimo virtuální síť | Některá nasazení cloudových služeb, která nejsou ve virtuální síti, se nepodporují pro migraci. <br><br> 1. použijte rozhraní API pro ověření, abyste zkontrolovali, jestli má nasazení nárok na migraci. <br> 2. Pokud je to možné, nasazení se přesunou do Azure Resource Manager v rámci virtuální sítě s předponou "DefaultRdfeVnet". | 
| Migrace nasazení, která obsahují nasazení produkčního i přípravného slotu pomocí dynamických IP adres | Migrace cloudové služby se dvěma sloty vyžaduje odstranění přípravného slotu. Po odstranění přípravného slotu migrujte produkční slot jako nezávislou cloudovou službu (rozšířenou podporu) v Azure Resource Manager. Potom znovu nasaďte pracovní prostředí jako novou cloudovou službu (rozšířenou podporu) a zajistěte její výměnu s prvními. | 
| Migrace nasazení, která obsahují nasazení produkčního i přípravného slotu pomocí Vyhrazená IP adresa adres | Nepodporováno | 
| Migrace produkčního a pracovního nasazení v jiné virtuální síti|Migrace cloudové služby se dvěma sloty vyžaduje odstranění přípravného slotu. Po odstranění přípravného slotu migrujte produkční slot jako nezávislou cloudovou službu (rozšířenou podporu) v Azure Resource Manager. Nasazení nové Cloud Services (rozšířené podpory) se dá propojit s migrovaným nasazením s povolenou vyměnitelné vlastnosti. Soubory nasazení starého přípravného slotu se dají znovu použít k vytvoření tohoto nového swapového nasazení. | 
| Migrace prázdné cloudové služby (cloudová služba bez nasazení) | Nepodporováno | 
| Migrace nasazení obsahujícího modul plug-in vzdálené plochy a rozšíření vzdálené plochy | Možnost 1: před migrací odeberte modul plug-in vzdálené plochy. To vyžaduje změny v souborech nasazení. Migrace pak bude procházet. <br><br> Možnost 2: odebrání rozšíření vzdálené plochy a migrace nasazení Po migraci odeberte modul plug-in a nainstalujte rozšíření. To vyžaduje změny v souborech nasazení. <br><br> Před migrací odeberte modul plug-in a rozšíření. [Moduly plug-in se nedoporučují](https://docs.microsoft.com/azure/cloud-services-extended-support/deploy-prerequisite#required-service-definition-file-csdef-updates) použít na Cloud Services (Rozšířená podpora).| 
| Virtuální sítě s nasazením PaaS i IaaS |Nepodporuje se <br><br> Přesuňte nasazení PaaS nebo IaaS do jiné virtuální sítě. Tato akce způsobí výpadek. | 
Nasazení cloudových služeb s využitím velikostí starších verzí (například malých nebo ExtraLarge). | Migrace se dokončí, ale velikosti rolí se aktualizují tak, aby používaly moderní velikosti rolí. Nedošlo k žádné změně ve vlastnostech cost nebo SKU a virtuální počítač se pro tuto změnu nerestartuje. Aktualizujte všechny artefakty nasazení tak, aby odkazovaly na tyto nové moderní velikosti rolí. Další informace najdete v tématu [dostupné velikosti virtuálních počítačů](available-sizes.md) .|
| Migrace cloudové služby do jiné virtuální sítě | Nepodporováno <br><br> 1. před migrací přesuňte nasazení na jinou klasickou virtuální síť. Tato akce způsobí výpadek. <br> 2. migrujte novou virtuální síť do Azure Resource Manager. <br><br> Nebo <br><br> 1. migrujte virtuální síť do Azure Resource Manager <br>2. přesuňte cloudovou službu do nové virtuální sítě. Tato akce způsobí výpadek. | 
| Cloudová služba ve virtuální síti, ale nemá přiřazenou explicitní podsíť | Nepodporováno Omezení rizik zahrnuje přesun role do podsítě, která vyžaduje restart role (výpadek). | 


## <a name="post-migration-changes"></a>Změny po migraci
Nasazení Cloud Services (Classic) se převede na nasazení cloudové služby (rozšířené podpory). Další podrobnosti najdete v [dokumentaci k Cloud Services (Rozšířená podpora)](deploy-prerequisite.md) .  

### <a name="changes-to-deployment-files"></a>Změny souborů nasazení 

V souboru. csdef a. cscfg zákazníka jsou provedeny menší změny, aby soubory nasazení splňovaly požadavky Azure Resource Manager a Cloud Services (Rozšířená podpora). Po migraci se načtou nové soubory nasazení nebo se aktualizují stávající soubory. To bude nutné pro operace aktualizace/odstranění.  

- Virtual Network používá úplné ID Azure Resource Manager prostředků namísto pouze názvu prostředku v části NetworkConfiguration souboru. cscfg. Například, `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. U virtuálních sítí patřících do stejné skupiny prostředků jako cloudová služba se můžete rozhodnout aktualizovat soubor. cscfg zpátky na jenom pomocí názvu virtuální sítě.  

- Klasické velikosti, jako je malá, Velká, ExtraLarge, se nahradí jejich novými názvy velikostí, Standard_A *. Názvy velikostí musí být v souboru. csdef změněny na nové názvy. Další informace najdete v tématu [požadavky nasazení Cloud Services (Rozšířená podpora)](deploy-prerequisite.md#required-service-definition-file-csdef-updates) .

- Pomocí rozhraní Get API získáte nejnovější kopii souborů nasazení. 
    - Získání šablony pomocí [portálu](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal), [PowerShellu](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell#export-resource-groups-to-templates), [CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli#export-resource-groups-to-templates)a [rozhraní REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate) 
    - Získejte soubor. csdef pomocí [prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    - Získejte soubor. cscfg pomocí [prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Změny pro automatizaci, CI/CD, kanály zákazníka, vlastní skripty, vlastní řídicí panely, vlastní nástroje atd.  

Zákazníci potřebují aktualizovat nástroje a automatizaci, aby mohli začít používat nová rozhraní API nebo příkazy ke správě svého nasazení. Zákazník může v rámci této změny snadno přijmout nové funkce a možnosti Azure Resource Manager/Cloud Services (Rozšířená podpora). 

- Změny názvů prostředků a skupin prostředků po migraci
    - V rámci migrace se u názvů několika prostředků, jako je cloudová služba, veřejné IP adresy atd., mění. Tyto změny možná budete muset v souborech nasazení projevit před aktualizací cloudové služby. [Přečtěte si další informace o názvech prostředků, které se mění](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- Znovu vytvořte pravidla a zásady potřebné ke správě a škálování cloudových služeb. 
    - [Pravidla automatického škálování](configure-scaling.md) se nemigrují. Po migraci znovu vytvořit pravidla automatického škálování.  
    - [Výstrahy](enable-alerts.md) nejsou migrovány. Po dokončení migrace znovu vytvořte výstrahy.
    - Key Vault se vytvoří bez jakýchkoli zásad přístupu. [Vytvořte v Key Vault příslušné zásady](../key-vault/general/assign-access-policy-portal.md) pro zobrazení nebo správu certifikátů. Certifikáty se budou zobrazovat v části nastavení na kartě s názvem tajné klíče.

## <a name="next-steps"></a>Další kroky
- [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migrace na Cloud Services (Rozšířená podpora) pomocí [Azure Portal](in-place-migration-portal.md)
- Migrace na Cloud Services (Rozšířená podpora) pomocí [PowerShellu](in-place-migration-powershell.md)
