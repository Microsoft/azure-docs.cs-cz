---
title: Co je privátní koncový bod Azure?
description: Seznamte se s privátním koncovým bodem Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 7394a9e1b1fa90741bc26fc0e9b6b7e6284d75e0
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104684"
---
# <a name="what-is-azure-private-endpoint"></a>Co je to privátní koncový bod Azure?

Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Služba může být služba Azure, například Azure Storage, SQL atd. nebo vaše vlastní [Služba privátních odkazů](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Vlastnosti privátního koncového bodu 
 Soukromý koncový bod určuje následující vlastnosti: 


|Vlastnost  |Description |
|---------|---------|
|Name    |    Jedinečný název v rámci skupiny prostředků.      |
|Subnet    |  Podsíť pro nasazení a přidělování privátních IP adres z virtuální sítě. Požadavky na podsíť najdete v části omezení v tomto článku.         |
|Prostředek privátního propojení    |   Prostředek privátního propojení pro připojení pomocí ID prostředku nebo aliasu ze seznamu dostupných typů. Pro veškerý provoz odeslaný do tohoto prostředku se vygeneruje jedinečný identifikátor sítě.       |
|Cílový podprostředek   |      Prostředek, který se má připojit. Každý typ prostředku privátního propojení má různé možnosti pro výběr na základě předvolby.    |
|Metoda schválení připojení    |  Automatické nebo ruční. V závislosti na oprávněních řízení přístupu na základě role (RBAC) může být váš soukromý koncový bod schválen automaticky. Pokud se pokusíte připojit k prostředku privátního propojení bez RBAC, použijte ruční metodu, která vlastníkovi prostředku povolí schválení připojení.        |
|Zpráva požadavku     |  Můžete zadat zprávu, aby požadovaná připojení byla schválena ručně. Tato zpráva se dá použít k identifikaci konkrétního požadavku.        |
|Stav připojení   |   Vlastnost jen pro čtení, která určuje, zda je privátní koncový bod aktivní. K odeslání provozu lze použít pouze privátní koncové body ve schváleném stavu. Další stavy k dispozici: <br>-**Schváleno**: Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití.</br><br>-**Čeká na vyřízení**: Připojení bylo vytvořeno ručně a uživatel čeká na schválení vlastníkem prostředku privátního odkazu.</br><br>-**Odmítnuto**: Připojení bylo odmítnuto vlastníkem prostředku privátního odkazu.</br><br>-**Odpojeno**: Připojení bylo odebráno vlastníkem prostředku privátního odkazu. Soukromý koncový bod se bude jednat o informativní a měl by se odstranit pro vyčištění. </br>|

Tady jsou některé klíčové podrobnosti o privátních koncových bodech: 
- Privátní koncový bod umožňuje připojení mezi spotřebiteli ze stejné virtuální sítě, v oblasti partnerských virtuální sítě, globálně vázaných virtuální sítě a místně pomocí [VPN](https://azure.microsoft.com/services/vpn-gateway/) nebo [Express Route](https://azure.microsoft.com/services/expressroute/) and Services využívajících soukromé odkazy.
 
- Při vytváření privátního koncového bodu se pro životní cyklus prostředku vytvoří také síťové rozhraní. Rozhraní je přiřazena privátní IP adresa z podsítě, která je mapována na službu privátního propojení.
 
- Privátní koncový bod musí být nasazený ve stejné oblasti jako virtuální síť. 
 
- Prostředek privátního propojení se dá nasadit v jiné oblasti, než je virtuální síť a soukromý koncový bod.
 
- Pomocí stejného prostředku privátního propojení lze vytvořit více privátních koncových bodů. V případě jedné sítě, která používá běžnou konfiguraci serveru DNS, doporučujeme použít jeden privátní koncový bod pro daný prostředek privátního propojení, aby nedocházelo k duplicitním položkám nebo konfliktům v překladu názvů DNS. 
 
- Ve stejné virtuální síti je možné vytvořit více privátních koncových bodů v rámci jedné nebo více podsítí. Existují omezení počtu privátních koncových bodů, které můžete v rámci předplatného vytvořit. Podrobnosti najdete v tématu [omezení Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits.md#networking-limits).


 
## <a name="private-link-resource"></a>Prostředek privátního propojení 
Prostředek privátního propojení je cílovým cílem daného privátního koncového bodu. Následuje seznam dostupných typů prostředků privátního propojení: 
 
|Název prostředku privátního propojení  |Typ prostředku   |Dílčí prostředky  |
|---------|---------|---------|
|**Služba privátního propojení** (Vaše vlastní služba)   |  Microsoft. Network/privateLinkServices       | Prázdná |
|**Azure SQL Database** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure SQL Data Warehouse** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure Storage**  | Microsoft.Storage/storageAccounts.    |  BLOB (objekt blob, blob_secondary)<BR> Table (tabulka; table_secondary)<BR> Queue (Queue; queue_secondary)<BR> Soubor (soubor, file_secondary)<BR> Web (Web; web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts.    |  BLOB (objekt blob, blob_secondary)       |
 
 
## <a name="network-security-of-private-endpoints"></a>Zabezpečení sítě privátních koncových bodů 
Při používání privátních koncových bodů pro služby Azure je provoz zabezpečený pro konkrétní prostředek privátního propojení. Platforma provede řízení přístupu, aby se ověřilo, že síťová připojení dosáhnou pouze zadaného prostředku privátního propojení. Pokud chcete získat přístup k dalším prostředkům v rámci stejné služby Azure, vyžadují se další privátní koncové body. 
 
Pro připojení k podporované službě Azure můžete úlohy zcela uzamknout z přístupu k veřejným koncovým bodům. Tento ovládací prvek zajišťuje další vrstvu zabezpečení sítě pro vaše prostředky tím, že poskytuje vestavěnou exfiltrace ochranu, která brání přístupu k dalším prostředkům hostovaným na stejné službě Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Přístup k prostředku privátního propojení pomocí pracovního postupu schválení 
K prostředku privátního propojení se můžete připojit pomocí následujících metod schvalování připojení:
- **Automaticky** schváleno, pokud vlastníte nebo máte oprávnění ke konkrétnímu prostředku privátního propojení. Požadovaná oprávnění jsou založená na typu prostředku privátního propojení v následujícím formátu: Microsoft. \<Poskytovatel >/< resource_type >/privateEndpointConnectionApproval/Action
- **Ruční** požadavek, pokud nemáte požadovaná oprávnění a přejete si požádat o přístup. Zahájí se schvalovací pracovní postup. Privátní koncový bod a následné připojení privátního koncového bodu se vytvoří ve stavu čeká na vyřízení. Vlastník prostředku privátního propojení je zodpovědný za schválení připojení. Po schválení je povolený privátní koncový bod pro normální odesílání provozu, jak je znázorněno v následujícím diagramu pracovního postupu schválení.  

![schválení pracovního postupu](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Vlastník prostředku privátního propojení může provést následující akce přes připojení privátního koncového bodu: 
- Zkontrolujte podrobnosti o všech připojeních privátního koncového bodu. 
- Schvalte připojení privátního koncového bodu. Odpovídající privátní koncový bod bude povolen k odeslání provozu do prostředku privátního propojení. 
- Odmítne připojení privátního koncového bodu. Odpovídající soukromý koncový bod bude aktualizován tak, aby odrážel stav.
- Odstraňte připojení privátního koncového bodu v libovolném stavu. Odpovídající privátní koncový bod bude aktualizován pomocí odpojeného stavu, aby odrážel akci, vlastník privátního koncového bodu může v tomto okamžiku odstranit pouze prostředek. 
 
> [!NOTE]
> Pouze privátní koncový bod ve schváleném stavu může odesílat provoz do daného prostředku privátního propojení. 

### <a name="connecting-using-alias"></a>Připojování pomocí aliasu
Alias je jedinečný moniker, který se generuje, když vlastník služby vytvoří službu privátního propojení za standardním nástrojem pro vyrovnávání zatížení. Vlastník služby může tento alias sdílet se svými spotřebiteli offline. Příjemci můžou požádat o připojení ke službě privátního propojení buď pomocí identifikátoru URI prostředku, nebo aliasu. Pokud se chcete připojit pomocí aliasu, musíte vytvořit privátní koncový bod pomocí metody ručního schválení připojení. Pro použití metody schvalování ručního připojení nastavte parametr ruční požadavek na hodnotu true během vytváření toku privátního koncového bodu. Podrobnosti najdete v [New-AzPrivateEndpoint](https://docs.microsoft.com/en-us/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) a [AZ Network Private-Endpoint Create](https://docs.microsoft.com/en-us/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) . 

## <a name="dns-configuration"></a>Konfigurace DNS 
Při připojování k prostředku privátního propojení pomocí plně kvalifikovaného názvu domény (FQDN) jako součásti připojovacího řetězce je důležité správně nakonfigurovat nastavení DNS tak, aby se přeložilo na přidělenou privátní IP adresu. Existující služby Azure už můžou mít konfiguraci DNS, která se má použít při připojování přes Veřejný koncový bod. To je nutné přepsat pro připojení pomocí privátního koncového bodu. 
 
Síťové rozhraní přidružené k privátnímu koncovému bodu obsahuje úplnou sadu informací potřebných ke konfiguraci DNS, včetně plně kvalifikovaného názvu domény a privátních IP adres přidělených pro daný prostředek privátního propojení. 
 
Pomocí následujících možností můžete nakonfigurovat nastavení DNS privátních koncových bodů: 
- **Použijte soubor hostitele (doporučeno pouze pro testování)** . K přepsání DNS můžete použít soubor hostitele na virtuálním počítači.  
- **Použijte privátní ZÓNU DNS**. K přepsání překladu DNS pro daný privátní koncový bod můžete použít soukromé zóny DNS. Privátní zóna DNS se dá propojit s vaší virtuální sítí a vyřešit konkrétní domény.
- **Použijte vlastní server DNS**. Pomocí vlastního serveru DNS můžete přepsat překlad DNS pro daný prostředek privátního propojení. Pokud je váš server DNS hostovaný ve virtuální síti, můžete vytvořit předávací pravidlo DNS pro použití privátní zóny DNS a zjednodušit tak konfiguraci všech prostředků privátního propojení.
 
> [!IMPORTANT]
> Nedoporučujeme potlačit zónu, která je aktivně používána k řešení veřejných koncových bodů. Připojení k prostředkům nebude možné správně přeložit bez předávání DNS do veřejného serveru DNS. Pokud se chcete vyhnout problémům, vytvořte jiný název domény nebo použijte navrhovaný název pro každou službu níže. 
 
Pro služby Azure použijte doporučené názvy zón, jak je popsáno v následující tabulce:

|Typ prostředku privátního propojení   |Vytváření  |Název zóny  |
|---------|---------|---------|
|SQL DB/DW (Microsoft. SQL/servery)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Účet úložiště (Microsoft. Storage/storageAccounts)    |  BLOB (objekt blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Účet úložiště (Microsoft. Storage/storageAccounts)    |    Table (tabulka; table_secondary)      |   privatelink.table.core.windows.net       |
|Účet úložiště (Microsoft. Storage/storageAccounts)    |    Queue (Queue; queue_secondary)     |   privatelink.queue.core.windows.net       |
|Účet úložiště (Microsoft. Storage/storageAccounts)   |    Soubor (soubor, file_secondary)      |    privatelink.file.core.windows.net      |
|Účet úložiště (Microsoft. Storage/storageAccounts)     |  Web (Web; web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake Gen2 systému souborů (Microsoft. Storage/storageAccounts)  |  Data Lake Gen2 systému souborů (DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
||||
 

Azure vytvoří záznam DNS (CNAME) kanonického názvu (CNAME) na veřejném serveru DNS pro přesměrování řešení na navržené názvy domén. Řešení bude možné přepsat privátní IP adresou vašich privátních koncových bodů. 
 
Vaše aplikace nemusí měnit adresu URL připojení. Při pokusu o překlad pomocí veřejné služby DNS se server DNS teď přeloží na vaše soukromé koncové body. Proces nemá vliv na vaše aplikace. 
 
## <a name="limitations"></a>Omezení
 
Následující tabulka obsahuje seznam známých omezení při použití privátních koncových bodů: 


|Omezení |Popis |Omezení rizik  |
|---------|---------|---------|
|Pravidla skupiny zabezpečení sítě (NSG) se nevztahují na soukromý koncový bod.    |NSG se nepodporuje u privátních koncových bodů. V případě, že k podsítím obsahujícím soukromý koncový bod může být přidruženo NSG, pravidla nebudou platná pro přenosy zpracovávané privátním koncovým bodem. K nasazení privátních koncových bodů v podsíti je nutné, aby bylo [vynucování zásad sítě zakázané](disable-private-endpoint-network-policy.md) . NSG se pořád vynutil na jiných úlohách hostovaných ve stejné podsíti.   | Řízení provozu pomocí pravidel NSG pro odchozí přenosy na zdrojových klientech.        |
|V podsítích povolených pro koncový bod služby nebo specializované úlohy nelze vytvořit privátní koncové body.    |V podsítích, které jsou povolené pro koncové body služby nebo podsítě delegované na specializované úlohy, nejde nasadit privátní koncové body.|  Vytvořte samostatnou podsíť pro nasazení privátních koncových bodů.        |
|privátní koncový bod se dá mapovat jenom na službu privátního propojení (vlastněné zákazníkem) ve stejné oblasti.    |   Připojení ke službě privátního propojení (vlastní) z jiné oblasti se nepodporuje.       |  Ve verzi Preview je nutné nasadit službu privátního propojení ve stejné oblasti.        |
|Specializované úlohy nemůžou přistupovat k privátním koncovým bodům.    |   Následující služby nasazené do vaší virtuální sítě nemají přístup k jakémukoli prostředku privátního propojení pomocí privátních koncových bodů:<br>Plán služby App Service</br>Instance kontejneru Azure</br>Azure NetApp Files</br>Rezervované HSM Azure<br>       |   Ve verzi Preview není žádné zmírnění.       |
|  Portál nepodporuje vytváření privátních koncových bodů pomocí aliasu.  |   Portál povoluje vytváření privátních koncových bodů pomocí identifikátoru URI prostředku.      | Pro vyžádání připojení privátních koncových bodů použít identifikátor URI prostředku        |

## <a name="next-steps"></a>Další postup
- [Vytvoření privátního koncového bodu pro SQL Database Server pomocí portálu](create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pro SQL Database Server pomocí PowerShellu](create-private-endpoint-powershell.md)
- [Vytvoření privátního koncového bodu pro SQL Database Server pomocí rozhraní příkazového řádku](create-private-endpoint-cli.md)
- [Vytvoření privátního koncového bodu pro účet úložiště pomocí portálu](create-private-endpoint-storage-portal.md)
- [Vytvoření vlastní služby privátního propojení pomocí Azure PowerShell](create-private-link-service-powershell.md)
