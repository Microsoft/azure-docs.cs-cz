---
title: Architektura OPC Vault – Azure | Dokumenty společnosti Microsoft
description: Architektura služby správy certifikátů OPC Vault
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1e08968034134e2b9ab3b8064387d18663d5c866
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "71200160"
---
# <a name="opc-vault-architecture"></a>Architektura OPC Vault

Tento článek poskytuje přehled o mikroslužbě OPC Vault a modulu OPC Vault IoT Edge.

Aplikace OPC UA používají certifikáty instancí aplikace k zajištění zabezpečení na úrovni aplikace. Zabezpečené připojení je navázáno pomocí asymetrické kryptografie, pro kterou aplikační certifikáty poskytují dvojici veřejného a soukromého klíče. Certifikáty mohou být podepsány vlastním podpisem nebo podepsány certifikační autoritou (CA).

Aplikace OPC UA obsahuje seznam důvěryhodných certifikátů, který představuje aplikace, kterým důvěřuje. Tyto certifikáty mohou být podepsány pod svým držitelem nebo podepsány certifikační autoritou nebo mohou být root-CA nebo sub-CA samy. Pokud je důvěryhodný certifikát součástí většího řetězu certifikátů, aplikace důvěřuje všem certifikátům, které se řetězí k certifikátu v seznamu důvěryhodných certifikátů. To platí tak dlouho, dokud lze ověřit celý řetěz certifikátů.

Hlavním rozdílem mezi důvěřováním certifikátům podepsaným svým držitelem a důvěřováním certifikátu certifikační autority je úsilí o instalaci potřebné k nasazení a udržení důvěryhodnosti. K dispozici je také další úsilí o hostování certifikační autority specifické pro společnost. 

Chcete-li distribuovat vztah důvěryhodnosti pro certifikáty podepsané svým držitelem pro více serverů s jednou klientskou aplikací, je nutné nainstalovat všechny certifikáty serverových aplikací do seznamu důvěryhodných certifikátů klientských aplikací. Kromě toho je nutné nainstalovat certifikát klientské aplikace do všech seznamů důvěryhodných certifikátů serverových aplikací. Toto administrativní úsilí je poměrně zátěž, a dokonce zvyšuje, když budete muset zvážit životnost certifikátu a obnovit certifikáty.

Použití certifikační autority specifické pro společnost může výrazně zjednodušit správu důvěryhodnosti s více servery a klienty. V takovém případě správce vygeneruje certifikát instance aplikace podepsaný certifikační autoritou jednou pro každého použitého klienta a serveru. Certifikát certifikační autority je navíc nainstalován v každém seznamu důvěryhodných certifikátů aplikací na všech serverech a klientech. S tímto přístupem je třeba obnovit a nahradit pouze certifikáty, jejichž platnost vypršela pro ohrožené aplikace.

Služba správy certifikátů Azure Industrial IoT OPC UA vám pomůže spravovat certifikační autoritu specifickou pro společnost pro aplikace OPC UA. Tato služba je založena na mikroslužbě OPC Vault. OPC Vault poskytuje mikroslužbu pro hostování certifikační autority specifické pro společnost v zabezpečeném cloudu. Toto řešení je zálohováno službami zabezpečenými službou Azure Active Directory (Azure AD), trezorem klíčů Azure s moduly hardwarového zabezpečení (HSM), Azure Cosmos DB a volitelně ioT hubem jako úložiště aplikací.

Mikroslužba OPC Vault je navržena tak, aby podporovala pracovní postup založený na rolích, kde správci zabezpečení a schvalovatelé s podpisovými právy v azure key vault ukládají nebo odmítají žádosti.

Pro kompatibilitu se stávajícími řešeními OPC UA, služby zahrnují podporu pro oPC Vault microservice couval edge modul. Tím se implementuje **OPC UA Global Discovery Server a rozhraní pro správu certifikátů** k distribuci certifikátů a seznamů důvěryhodných certifikátů podle části 12 specifikace. 


## <a name="architecture"></a>Architektura

Architektura je založena na mikroslužbě OPC Vault s modulem OPC Vault IoT Edge pro tovární síť a webovým ukázkovým ux pro řízení pracovního postupu:

![Diagram architektury OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Mikroservis OPC Vault

Mikroslužba OPC Vault se skládá z následujících rozhraní pro implementaci pracovního postupu pro distribuci a správu certifikační autority specifické pro opc ua aplikace.

### <a name="application"></a>Aplikace 
- Aplikace OPC UA může být server nebo klient nebo obojí. OPC Vault slouží v tomto případě jako orgán pro registraci aplikací. 
- Kromě základních operací pro registraci, aktualizaci a zrušení registrace aplikací existují také rozhraní pro vyhledávání a dotazování na aplikace s vyhledávacími výrazy. 
- Žádosti o certifikát musí odkazovat na platnou žádost, aby bylo možné žádost zpracovat a vystavit podepsaný certifikát se všemi rozšířeními specifickými pro OPC UA. 
- Aplikační služba je zálohována databází v Azure Cosmos DB.

### <a name="certificate-group"></a>Skupina certifikátů
- Skupina certifikátů je entita, ve které je uložen kořenový certifikační autorita nebo dílčí certifikát certifikační autority, včetně soukromého klíče pro podepisování certifikátů. 
- Délka klíče RSA, délka hash SHA-2 a životnost jsou konfigurovatelné jak pro certifikační autoritu vystavitela, tak pro podepsané certifikáty aplikací. 
- Certifikáty certifikační autority ukládáte do trezoru klíčů Azure, který je podpořený softwarem zabezpečení 140-2 úrovně 2. Soukromý klíč nikdy neopustí zabezpečené úložiště, protože podepisování se provádí pomocí operace trezoru klíčů zabezpečené službou Azure AD. 
- Certifikáty certifikační autority můžete v průběhu času obnovit a nechat je v bezpečném úložišti kvůli historii trezoru klíčů. 
- Seznam odvolání pro každý certifikát certifikační autority je také uložen v trezoru klíčů jako tajný klíč. Pokud je aplikace neregistrovaná, je certifikát aplikace odvolán v seznamu odvolaných certifikátů (CRL) správcem.
- Můžete odvolat jednotlivé certifikáty, stejně jako dávkové certifikáty.

### <a name="certificate-request"></a>Žádost o certifikát
Žádost o certifikát implementuje pracovní postup ke generování nového páru klíčů nebo podepsaného certifikátu pomocí žádosti o podpis certifikátu (CSR) pro aplikaci OPC UA. 
- Požadavek je uložen v databázi s doprovodnými informacemi, jako je předmět nebo zástupce společnosti, a odkazem na aplikaci OPC UA. 
- Obchodní logika ve službě ověřuje požadavek proti informacím uloženým v databázi aplikace. Například aplikace Uri v databázi musí odpovídat aplikaci Uri v csr.
- Správce zabezpečení s podpisovými právy (tj. role Schvalovatel) žádost schválí nebo zamítne. Pokud je žádost schválena, je vygenerován nový pár klíčů nebo podepsaný certifikát (nebo obojí). Nový soukromý klíč je bezpečně uložen v trezoru klíčů a nový podepsaný veřejný certifikát je uložen v databázi žádosti o certifikát.
- Žadatel může dotazovat stav požadavku, dokud není schválen nebo odvolán. Pokud byla žádost schválena, soukromý klíč a certifikát lze stáhnout a nainstalovat do úložiště certifikátů aplikace OPC UA.
- Žadatel nyní může přijmout požadavek na odstranění nepotřebných informací z databáze požadavků. 

Po dobu životnosti podepsaného certifikátu může být aplikace odstraněna nebo může dojít k ohrožení zabezpečení klíče. V takovém případě může správce certifikační autority:
- Odstraňte aplikaci, která také odstraní všechny nevyřízené a schválené žádosti o certifikát aplikace. 
- Odstraňte pouze jednu žádost o certifikát, pokud je obnoven nebo ohrožen pouze klíč.

Nyní jsou schválené a přijaté žádosti o certifikát označeny jako odstraněné.

Manažer může pravidelně obnovovat seznam CRL certifikační autority vystavithona. V době obnovení jsou všechny odstraněné žádosti o certifikát odvolány a sériová čísla certifikátů jsou přidána do seznamu odvolaných certifikátů. Odvolané žádosti o certifikát jsou označeny jako odvolané. V naléhavých událostech lze odvolat také jednotlivé žádosti o certifikát.

A konečně, aktualizované seznamy CRL jsou k dispozici pro distribuci zúčastněným klientům a serverům OPC UA.

## <a name="opc-vault-iot-edge-module"></a>Modul OPC Vault IoT Edge
Chcete-li podporovat globální server zjišťování v tovární síti, můžete nasadit modul OPC Vault na hraničních zařízeních. Spusťte ji jako místní aplikaci .NET Core nebo ji spusťte v kontejneru Dockeru. Všimněte si, že z důvodu nedostatku podpory ověřování Auth2 v aktuálním zásobníku OPC UA .NET Standard je funkce hraničního modulu OPC Vault omezena na roli čtečky. Uživatel nemůže být zosobněn z hraničního modulu do mikroslužby pomocí standardního rozhraní OPC UA GDS.

## <a name="next-steps"></a>Další kroky

Nyní, když jste se dozvěděli o architektuře OPC Vault, můžete:

> [!div class="nextstepaction"]
> [Sestavení a nasazení opc trezoru](howto-opc-vault-deploy.md)
