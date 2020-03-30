---
title: Běžné otázky pro síť Azure Service Fabric Mesh
description: Přečtěte si o často kladených otázkách a odpovědích pro Azure Service Fabric Mesh.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 2a5c2ea63d162eb6fb78ab702e0519f8ac25dcc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252493"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Běžně kladené otázky service fabric mesh

Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Tento článek obsahuje odpovědi na často kladené otázky.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Jak nahlásím problém nebo položím otázku?

Ptejte se, získejte odpovědi od techniků microsoftu a nahlaste problémy v [úložišti GitHub](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Kvóta a náklady

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Jaké jsou náklady na účast ve verzi Preview?

V současné době nejsou žádné poplatky za nasazení aplikací nebo kontejnerů do náhledu sítě. Prosím, sledujte aktualizace v květnu pro povolení pro fakturaci. Doporučujeme vám však odstranit prostředky, které nasazujete, a nenechat je spuštěné, pokud je aktivně netestujete.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Existuje limit kvóty počtu jader a paměti RAM?

Ano. Kvóty pro každé předplatné jsou:

- Počet žádostí: 5
- Jader na aplikaci: 12
- Celková ram na aplikaci: 48 GB
- Koncových bodů sítě a příchozího přenosu dat: 5
- Svazky Azure, které můžete připojit: 10
- Počet replik služby: 3
- Největší kontejner, který můžete nasadit, je omezen na 4 jádra a 16GB RAM.
- Částečné jádra můžete přidělit kontejnerům v krocích po 0,5 jádrech, maximálně však 6 jader.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Jak dlouho mohu nechat aplikaci nasazenou?

V současné době jsme omezili životnost aplikace na dva dny. To je s cílem maximalizovat využití volných jader přidělených náhledu. V důsledku toho můžete spustit pouze dané nasazení nepřetržitě po dobu 48 hodin, po které bude vypnuto.

Pokud se to stane, můžete ověřit, že systém `az mesh app show` jej vypnout spuštěním příkazu v nastavení příkazu Azure. Zkontrolujte, zda se vrátí`"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Například: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
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

Chcete-li odstranit skupinu `az group delete <nameOfResourceGroup>` prostředků, použijte příkaz.

## <a name="deployments"></a>Nasazení

### <a name="what-container-images-are-supported"></a>Jaké image kontejnerů jsou podporovány?

Pokud vyvíjíte na Windows Fall Creators Update (verze 1709) počítač, můžete použít pouze Windows verze 1709 docker image.

Pokud vyvíjíte na počítači s aktualizací windows 10 z dubna 2018 (verze 1803), můžete použít buď windows verze 1709 nebo windows verze 1803 docker image.

K nasazení služeb lze použít následující image operačního operačního centra kontejneru:
- Windows - windowsservercore a nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Žádná známá omezení

> [!NOTE]
> Nástroje Visual Studio pro mesh zatím nepodporují nasazení do kontejnerů Windows Server 2019 a 1809.

### <a name="what-types-of-applications-can-i-deploy"></a>Jaké typy aplikací lze nasadit? 

Můžete nasadit cokoli, co běží v kontejnerech, které se vejdou do omezení kladenna na prostředek aplikace (viz výše další informace o kvótách). Pokud zjistíme, že používáte Mesh pro spouštění nelegálních úloh nebo zneužívání systému (tj. těžba), vyhrazujeme si právo ukončit vaše nasazení a zablokovat spuštění vašeho předplatného ve službě. Pokud máte nějaké dotazy ohledně spuštění konkrétního pracovního vytížení, obraťte se na nás. 

## <a name="developer-experience-issues"></a>Problémy s vývojářskými zkušenostmi

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Rozlišení DNS z kontejneru nefunguje

Odchozí dotazy DNS z kontejneru do služby DNS Service Fabric může za určitých okolností selhat. Tohle se vyšetřuje. Chcete-li zmírnit:

- Jako základní bitovou kopii kontejneru použijte aktualizaci Windows Fall Creators (verze 1709) nebo vyšší.
- Pokud samotný název služby nefunguje, zkuste plně kvalifikovaný název: ServiceName.ApplicationName.
- V souboru Dockeru pro `EXPOSE <port>` vaši službu přidejte, kde port je port, na kterém vystavujete službu. Například:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>Služba DNS nefunguje stejně jako u vývojových clusterů Service Fabric a v síti Mesh

Možná budete muset odkazovat na služby odlišně v clusteru místního vývoje než v Azure Mesh.

V clusteru místního vývoje použijte `{serviceName}.{applicationName}`. V azure service fabric `{servicename}`mesh použijte . 

Azure Mesh momentálně nepodporuje rozlišení DNS napříč aplikacemi.

Další známé problémy se službou DNS se spuštěním vývojového clusteru Service Fabric ve Windows 10 najdete v [tématu Ladění kontejnerů systému Windows](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) a [známých problémů se službou DNS](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Síťové služby

ServiceFabric sítě NAT může zmizet při spuštění aplikace v místním počítači. Chcete-li diagnostikovat, zda k tomu došlo, spusťte z příkazového řádku následující:

`docker network ls`a všimněte si, zda `servicefabric_nat` je uveden.  Pokud ne, spusťte následující příkaz:`docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

To bude řešit problém i v případě, že aplikace je již nasazena místně a v stavu není v pořádku.

### <a name="issues-running-multiple-apps"></a>Problémy se spuštěním více aplikací

Může dojít k dostupnosti procesoru a omezení jsou stanoveny ve všech aplikacích. Chcete-li zmírnit:
- Vytvořte cluster pěti uzlů.
- Snižte využití procesoru ve službách v celé aplikaci, která je nasazená. Například v souboru service.yaml vaší `cpu: 1.0` služby změňte na`cpu: 0.5`

Do clusteru s jedním uzlem nelze nasadit více aplikací. Chcete-li zmírnit:
- Při nasazování více aplikací do místního clusteru použijte cluster s pěti uzly.
- Odeberte aplikace, které právě netestujete.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>Nástroj vs má omezenou podporu pro kontejnery systému Windows

Nástroje Visual Studio podporují pouze nasazení kontejnerů Windows se základní verzí operačního systému Windows Server 1709 a 1803 dnes. 

## <a name="feature-gaps-and-other-known-issues"></a>Mezery v funkcích a další známé problémy

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Po nasazení aplikace nemá síťový prostředek, který je k ní přidružen, adresu IP

Existuje známý problém, kdy ip adresa není k dispozici okamžitě. Zkontrolujte stav síťového prostředku během několika minut a zobceji přidruženou adresu IP.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Aplikace se nezdaří přístup k správné síti /svazku prostředku

V modelu aplikace použijte úplné ID prostředku pro sítě a svazky, abyste měli přístup k přidruženému prostředku. Zde je příklad z ukázky rychlého startu:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Když horizontální navýšení kapacity, všechny mé kontejnery jsou ovlivněny, včetně těch, které běží

Jedná se o chybu a je implementována oprava.

## <a name="next-steps"></a>Další kroky

Další informace o síti Service Fabric mesh naleznete v [přehledu](service-fabric-mesh-overview.md).
