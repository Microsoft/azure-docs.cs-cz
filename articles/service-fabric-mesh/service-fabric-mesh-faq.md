---
title: Běžné otázky týkající se sítě Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o nejčastější dotazy a odpovědi pro Azure Service Fabric mřížky.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 12/12/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 8d6dcdd4de8fb56935d2f66bfff045b2115d4004
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063569"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Nejčastější dotazy služby prostředků infrastruktury sítě

Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Tento článek obsahuje odpovědi na nejčastější dotazy.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Jak ohlásit problém nebo dotaz?

Ptejte se, získejte odpovědi od odborníků Microsoftu a hlášení problémů v [úložiště GitHub service-fabric sítě preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Kvóty a nákladů

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Kolik stojí účast ve verzi preview?

Aktuálně se nic neplatí pro nasazování aplikací nebo kontejnerů sítě ve verzi Preview. Ale doporučujeme vám odstranit prostředky, nasadit a ne nechat běžet je aktivně testování.

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

## <a name="supported-container-os-images"></a>Image kontejnerů podporovaný operační systém

Pokud vyvíjíte na počítači s Windows Fall Creators Update (verze 1709), můžete použít pouze Image dockeru s aplikací Windows verze 1709.

Pokud vyvíjíte ve Windows 10. dubna 2018 update (verze 1803) počítače, můžete použít Windows verze 1709 nebo imagí dockeru verze 1803 Windows.

Následující Image kontejneru operačních systémů lze použít k nasazení služeb:

- Windows – windowsservercore a nanoserver
    - Windows Server verze 1709
    - Windows Server verze 1803
- Linux
    - Žádné známé omezení

## <a name="developer-experience-issues"></a>Problémy prostředí pro vývojáře

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Překlad DNS z kontejneru nebude fungovat.

Za určitých okolností může selhat odchozí dotazy DNS z kontejneru ve službě Service Fabric DNS. To je Zkoumáme. Zmírnit:

- Použití Windows Fall Creators update (verze 1709) nebo vyšší jako image základního kontejneru.
- Pokud název služby samostatně nefunguje, zkuste plně kvalifikovaný název: ServiceName.ApplicationName.
- V souboru Docker pro vaši službu, přidejte `EXPOSE <port>` tam, kde je port jsou vystavení služby v. Příklad:

```DockerFile
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
