---
title: Běžné otázky týkající se sítě Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o nejčastější dotazy a odpovědi pro Azure Service Fabric mřížky.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 950f9ac89b9d3224db29b32fe2d1e403ccc98116
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143281"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Nejčastější dotazy služby prostředků infrastruktury sítě

Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Tento článek obsahuje odpovědi na nejčastější dotazy.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Jak ohlásit problém nebo dotaz?

Ptejte se, získejte odpovědi od odborníků Microsoftu a hlášení problémů v [úložiště GitHub service-fabric sítě preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Kvóty a nákladů

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Kolik stojí účast ve verzi preview?

Aktuálně neúčtují žádné poplatky pro nasazování aplikací nebo kontejnerů na síť ve verzi preview. Aktualizace v květnu sledujte podpora pro fakturaci. Ale doporučujeme vám odstranit prostředky, nasadit a ne nechat běžet je aktivně testování.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Existuje nějaké omezení kvóty počtu jader a paměti RAM?

Ano. Kvóty pro každé předplatné jsou:

- Počet aplikací: 5
- Počet jader na aplikace: 12
- Celkové paměti RAM na aplikaci: 48 GB
- Koncové body sítě a příchozího přenosu dat: 5
- Azure svazky, které lze připojit: 10
- Počet replik služby: 3
- Největší kontejner, který můžete nasadit je omezena na 4 jádra a 16 GB paměti RAM.
- Kontejnery v přírůstcích po 0,5 jádra, až do maximálního počtu jader 6, kterou můžete přidělit částečné jader.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Jak dlouho je můžete nechat aplikaci nasadit?

Právě jsme mají omezenou životnost aplikace do dvou dnů. Je to kvůli maximalizovat využití volných jader přidělené ve verzi Preview. V důsledku toho můžete jsou povolené jenom pro spuštění daného nasazení průběžně po dobu 48 hodin, po dobu se ukončí.

Pokud se to stát, můžete ověřit, že systém vypnout spuštěním `az mesh app show` příkaz v rozhraní příkazového řádku Azure. Zaškrtněte, pokud chcete zjistit, zda vrací `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Příklad: 

```cli
~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Chcete-li odstranit skupinu prostředků, použijte `az group delete <nameOfResourceGroup>` příkazu.

## <a name="deployments"></a>Nasazení

### <a name="what-container-images-are-supported"></a>Jaké Image kontejnerů jsou podporovány?

Pokud vyvíjíte na počítači s Windows Fall Creators Update (verze 1709), můžete použít pouze Image dockeru s aplikací Windows verze 1709.

Pokud vyvíjíte ve Windows 10. dubna 2018 update (verze 1803) počítače, můžete použít Windows verze 1709 nebo imagí dockeru verze 1803 Windows.

Následující Image kontejneru operačních systémů lze použít k nasazení služeb:

- Windows – windowsservercore a nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Žádné známé omezení

> [!NOTE]
> Nástroje pro síť sady Visual Studio zatím nepodporuje nasazování do systému Windows Server 2019 a 1809 kontejnerů.

### <a name="what-types-of-applications-can-i-deploy"></a>Jaké typy aplikací, které můžete nasadit 

Můžete nasadit cokoli, co běží v kontejnerech, které se vejdou do omezení umístí na prostředek aplikace (viz výše pro další informace o kvótách). Pokud zjistíme, že používáte síť pro spouštění úloh v neplatné nebo zneužije systému (to znamená dolování), pak vyhrazujeme si právo k ukončení nasazení a seznamu blokovaných předplatného ve spuštění ve službě. Kontaktujte nás Pokud máte nějaké dotazy na konkrétní úlohu. 

## <a name="developer-experience-issues"></a>Problémy prostředí pro vývojáře

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Překlad DNS z kontejneru nebude fungovat.

Za určitých okolností může selhat odchozí dotazy DNS z kontejneru ve službě Service Fabric DNS. To je Zkoumáme. Zmírnit:

- Použití Windows Fall Creators update (verze 1709) nebo vyšší jako image základního kontejneru.
- Pokud název služby samostatně nefunguje, zkuste plně kvalifikovaný název: ServiceName.ApplicationName.
- V souboru Docker pro vaši službu, přidejte `EXPOSE <port>` tam, kde je port jsou vystavení služby v. Příklad:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS nefunguje stejné jako pro vývoj clustery Service Fabric a na síť

Budete muset odkazovat služby jinak ve vašem místním vývojovém clusteru než v sítě Azure.

Ve vašem místním vývojovém clusteru použít `{serviceName}.{applicationName}`. V Azure Service Fabric sítě pomocí `{servicename}`. 

Síť Azure aktuálně nepodporuje překlad názvů DNS v aplikacích.

Další známých problémů s DNS se spouštěním vývojový cluster Service Fabric ve Windows 10 najdete tady: [Ladění kontejnerů Windows](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) a [známé problémy s DNS](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Sítě

ServiceFabric síti NAT může zmizí při použití vaše aplikace běžela na místním počítači. To zjistit, jestli se to stalo, spusťte z příkazového řádku následující:

`docker network ls` a Všimněte si, zda `servicefabric_nat` je uvedena.  Pokud ne, spusťte následující příkaz: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

I v případě, že aplikace už je nasazená místně a v pořádku, bude to vyřešit problém.

### <a name="issues-running-multiple-apps"></a>Problémy s více aplikacemi

Může dojít k dostupnosti procesoru a mezích ve všech aplikacích. Zmírnit:
- Vytvořte cluster s pěti uzly.
- Snížit využití procesoru v služeb v aplikaci, která je nasazena. Například v souboru service.yaml svojí služby, změňte `cpu: 1.0` do `cpu: 0.5`

Více aplikací nejde nasadit do clusteru s jedním uzlem. Zmírnit:
- Při nasazování více aplikací do místního clusteru, použijte cluster s pěti uzly.
- Odebrání aplikací, které nejsou aktuálně testování.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>VS nástrojů má omezenou podporu pro kontejnery Windows

Nástroje sady Visual Studio podporuje pouze nasazení kontejnerů Windows v základní operační systém verzi Windows serveru 1709 a 1803 ještě dnes. 

## <a name="feature-gaps-and-other-known-issues"></a>Funkce a dalších známých problémech

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Po nasazení aplikace, nemá síťový prostředek přidružené IP adresy

Existuje známý problém, do které IP adresa není k dispozici ihned. Zkontrolujte stav síťový prostředek za pár minut zobrazíte přidruženou IP adresu.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Moje aplikace nepodaří získat přístup k prostředku správné síti nebo svazku

Ve vašem aplikačním modelu použijte úplné ID prostředku pro sítě a svazků bude mít přístup k přidružený prostředek. Tady je příklad z ukázky rychlý start:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Když mám horizontální navýšení kapacity, všechny moje kontejnerů se vztahuje, včetně těch, které jsou spuštění

Jedná se chybu a opravu se implementuje.

## <a name="next-steps"></a>Další postup

Další informace o Service Fabric mřížky, [přehled](service-fabric-mesh-overview.md).
