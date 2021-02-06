---
title: Běžné otázky pro síť Azure Service Fabric
description: Seznamte se s nejčastějšími dotazy a odpověďmi na Azure Service Fabric sítě.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 8e53ab0ae4cc463bea8a6a8cb6d339f94fdcac6d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626032"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Nejčastější dotazy týkající se Service Fabric sítě

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Tento článek obsahuje odpovědi na nejčastější dotazy.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Návody nahlásit problém nebo položit otázku?

Položte otázky, Získejte odpovědi od techniků Microsoftu a ohlaste problémy v [úložišti GitHub Service-Fabric-mřížka-Preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Kvóta a náklady

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Jaké jsou náklady na účast ve verzi Preview?

V tuto chvíli nejsou žádné poplatky za nasazení aplikací nebo kontejnerů do sítě ve verzi Preview. Sledujte prosím aktualizace, které se můžou povolit pro účely fakturace. Doporučujeme však odstranit prostředky, které nasadíte, a nenechat je spuštěny, pokud je aktivně netestujete.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Je k dispozici limit kvóty počtu jader a paměti RAM?

Ano. Kvóty pro každé předplatné jsou:

- Počet aplikací: 5
- Počet jader na aplikaci: 12
- Celková velikost paměti RAM na aplikaci: 48 GB
- Koncové body sítě a příchozího přenosu dat: 5
- Svazky Azure, které můžete připojit: 10
- Počet replik služby: 3
- Největší kontejner, který můžete nasadit, je omezený na 4 jádra a 16GB RAM.
- Do kontejnerů můžete rozdělit částečné jádra v přírůstcích po 0,5 jader, až do maximálního počtu 6 jader.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Jak dlouho můžu aplikaci nechat nasazenou?

V současné době jsme omezili životnost aplikace na dva dny. Je to kvůli maximalizaci používání volných jader přidělených ve verzi Preview. V důsledku toho je povoleno spouštět dané nasazení nepřetržitě po dobu 48 hodin, po uplynutí této doby bude ukončena.

Pokud se to zobrazí, můžete ověřit, že systém ukončí činnost spuštěním `az mesh app show` příkazu v rozhraní příkazového řádku Azure CLI. Zkontrolujte, jestli se vrátí. `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Příklad: 

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

Pokud chcete odstranit skupinu prostředků, použijte `az group delete <nameOfResourceGroup>` příkaz.

## <a name="deployments"></a>Nasazení

### <a name="what-container-images-are-supported"></a>Jaké image kontejnerů se podporují?

Pokud vyvíjíte na počítači se systémem Windows Update Creators Update (verze 1709), můžete použít pouze image Windows verze 1709 Docker.

Pokud vyvíjíte na počítači s Windows 10. dubna 2018 Update (verze 1803), můžete použít image Windows verze 1709 nebo Windows verze 1803 Docker.

K nasazení služeb je možné použít následující image operačního systému kontejnerů:
- Windows – windowsservercore a nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Žádná známá omezení

> [!NOTE]
> Nástroje sady Visual Studio pro síť ještě nepodporují nasazování do kontejnerů Windows Server 2019 a 1809.

### <a name="what-types-of-applications-can-i-deploy"></a>Jaké typy aplikací můžu nasadit? 

Můžete nasadit cokoli, co běží v kontejnerech, které vyhovují omezením uvedeným v prostředku aplikace (Další informace o kvótách najdete výše). Pokud zjistíme, že používáte síť pro spouštění neplatných úloh nebo zneužití systému (například dolování), vyhrazujeme si právo ukončit vaše nasazení a seznamu blokovaných vaše předplatné na používání služby. Pokud máte nějaké dotazy ke spuštění konkrétního zatížení, obraťte se na nás. 

## <a name="developer-experience-issues"></a>Problémy s vývojářským prostředím

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Překlad DNS z kontejneru nefunguje

Odchozí dotazy DNS z kontejneru do služby Service Fabric DNS za určitých okolností nemusí selhat. Probíhá šetření. Pro zmírnění:

- Použijte Windows Update Creators Update (verze 1709) nebo vyšší jako základní image kontejneru.
- Pokud název služby samotný nefunguje, zkuste plně kvalifikovaný název: ServiceName. ApplicationName.
- V souboru Docker pro vaši službu přidejte port, `EXPOSE <port>` na kterém je port, na kterém službu zveřejňujete. Příklad:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>Služba DNS nefunguje stejně jako u Service Fabricch vývojových clusterů a v síti.

Možná budete muset v místním vývojovém clusteru odkázat na jiné služby než v Azure.

V místním vývojovém clusteru použijte `{serviceName}.{applicationName}` . V Azure Service Fabric sítě použijte `{servicename}` . 

Síť Azure v současné době nepodporuje překlad DNS napříč aplikacemi.

Další známé problémy se službou DNS při spuštění Service Fabric vývojového clusteru ve Windows 10 najdete v tématu: [ladění kontejnerů Windows](../service-fabric/service-fabric-how-to-debug-windows-containers.md) a [známých problémů se službou DNS](../service-fabric/service-fabric-dnsservice.md#known-issues).

### <a name="networking"></a>Sítě

NAT sítě ServiceFabric může zmizet při používání aplikace na vašem místním počítači. Chcete-li diagnostikovat, zda k tomu došlo, spusťte z příkazového řádku následující příkaz:

`docker network ls` a Všimněte si, zda `servicefabric_nat` je uvedena v seznamu.  Pokud ne, spusťte následující příkaz: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Tím se problém vyřeší i v případě, že je aplikace už nasazená místně a ve stavu není v pořádku.

### <a name="issues-running-multiple-apps"></a>Problémy s provozem více aplikací

Můžete narazit na dostupnost procesoru a omezení, které jsou ve všech aplikacích vyřešené. Pro zmírnění:
- Vytvořte cluster s pěti uzly.
- Snižte využití CPU ve službách napříč nasazenou aplikací. Například v souboru Service. yaml vaší služby změňte `cpu: 1.0` na `cpu: 0.5`

Do clusteru s jedním uzlem nelze nasadit více aplikací. Pro zmírnění:
- Při nasazování více aplikací do místního clusteru použijte cluster s pěti uzly.
- Odeberte aplikace, které aktuálně netestujete.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>Nástroje VS mají omezené podpory pro kontejnery Windows.

Nástroje sady Visual Studio podporují nasazení kontejnerů Windows jenom se základní verzí operačního systému Windows Server 1709 a 1803 ještě dnes. 

## <a name="feature-gaps-and-other-known-issues"></a>Mezery funkcí a další známé problémy

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Po nasazení aplikace není k síťovému prostředku, který je k němu přidružená, IP adresa.

Došlo k známému problému, při kterém se IP adresa nestane hned k dispozici. Pokud chcete zobrazit přidruženou IP adresu, zkontrolujte stav síťového prostředku během několika minut.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Aplikaci se nepodařilo získat přístup ke správnému prostředku sítě nebo svazku

V modelu aplikace použijte úplné ID prostředku pro sítě a svazky, abyste měli přístup k přidruženému prostředku. Tady je příklad z ukázky rychlého startu:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Při horizontálním navýšení kapacity budou ovlivněny všechny moje kontejnery, včetně spuštěných.

Jedná se o chybu a je implementována oprava.

## <a name="next-steps"></a>Další kroky

Další informace o Service Fabric sítě najdete v [přehledu](service-fabric-mesh-overview.md).
