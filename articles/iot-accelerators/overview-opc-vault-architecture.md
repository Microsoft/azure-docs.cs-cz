---
title: Architektura úložiště OPC – Azure | Microsoft Docs
description: Architektura služby správy certifikátů úložiště OPC
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: eb558d967ad657d14158684fba92b13979ea5fe2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646709"
---
# <a name="opc-vault-architecture"></a>Architektura úložiště OPC

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Tento článek poskytuje přehled o mikroslužbě trezoru OPC a modulu OPC pro trezor IoT Edge.

Aplikace OPC UA používají certifikáty instance aplikace k zajištění zabezpečení na úrovni aplikace. Zabezpečené připojení je vytvořeno pomocí asymetrické kryptografie, pro kterou certifikáty aplikací poskytují pár veřejného a privátního klíče. Certifikáty můžou být podepsané svým držitelem nebo podepsaný certifikační autoritou (CA).

Aplikace OPC UA obsahuje seznam důvěryhodných certifikátů, které představují aplikace, kterým důvěřují. Tyto certifikáty můžou být podepsané svým držitelem nebo podpisem certifikační autority, nebo se může jednat o samostatnou certifikační AUTORITu nebo podřízenou certifikační autoritu. Pokud je důvěryhodný certifikát součástí většího řetězu certifikátů, aplikace považuje všechny certifikáty, které jsou zřetězené do certifikátu, v seznamu důvěryhodných certifikátů. To platí, pokud je možné ověřit úplný řetěz certifikátů.

Hlavním rozdílem mezi důvěryhodnými certifikáty podepsanými držitelem a důvěryhodným certifikátem certifikační autority je úsilí při instalaci potřebné k nasazení a údržbě důvěryhodnosti. Existuje také další úsilí pro hostování certifikační autority specifické pro společnost. 

Chcete-li distribuovat důvěru certifikátů podepsaných svým držitelem pro více serverů s jednou klientskou aplikací, je nutné nainstalovat všechny certifikáty serverových aplikací v seznamu důvěryhodných klientských aplikací. Kromě toho je nutné nainstalovat certifikát klientské aplikace do všech seznamů důvěryhodných serverových aplikací. Toto administrativní úsilí je poměrně náročné a dokonce se zvyšuje, když je potřeba zvážit životnost certifikátů a obnovování certifikátů.

Použití certifikační autority specifické pro společnost může značně zjednodušit správu důvěryhodnosti s více servery a klienty. V takovém případě správce vygeneruje certifikát instance aplikace podepsaný certifikační autoritou pro každého klienta a server, který se používá. Certifikát certifikační autority se navíc nainstaluje do všech seznamů důvěryhodných aplikací na všech serverech a klientech. S tímto přístupem je potřeba obnovit a nahradit ovlivněné aplikace jenom certifikáty, jejichž platnost vypršela.

Služba Azure průmyslový IoT OPC UA Certificate Management umožňuje spravovat certifikační autoritu pro aplikace OPC UA pro konkrétní společnost. Tato služba je založená na mikroslužbě trezoru OPC. OPC trezor poskytuje mikroslužbu pro hostování certifikační autority specifické pro společnost v zabezpečeném cloudu. Toto řešení je zajištěno službami zabezpečenými službou Azure Active Directory (Azure AD), Azure Key Vault pomocí modulů hardwarového zabezpečení (HSM), Azure Cosmos DB a volitelně IoT Hub jako úložiště aplikace.

Mikroslužba trezoru OPC je navržená tak, aby podporovala pracovní postup založený na rolích, kde se správci zabezpečení a schvalovatelé s přihlašovacími právy v Azure Key Vault schvalují nebo zamítají žádosti.

Pro zajištění kompatibility se stávajícími řešeními OPC UA zahrnují služby podporu pro modul back-(OPC) pro trezor mikroslužeb. Tím se implementuje **globální zjišťování serveru OPC UA a rozhraní pro správu certifikátů** pro distribuci certifikátů a seznamů důvěryhodných certifikátů podle části 12 specifikace. 


## <a name="architecture"></a>Architektura

Architektura je založena na mikroslužbě trezoru OPC s modulem IoT Edge OPC pro správu a webovým prostředím, které slouží k řízení pracovního postupu:

![Diagram architektury OPC trezoru](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Mikroslužba trezoru OPC

Mikroslužba trezoru OPC se skládá z následujících rozhraní k implementaci pracovního postupu pro distribuci a správu certifikační autority specifické pro společnost pro aplikace OPC UA.

### <a name="application"></a>Aplikace 
- Aplikace OPC UA může být server nebo klient, nebo obojí. OPC trezor slouží jako registrační autorita aplikace v tomto případě. 
- Kromě základních operací pro registraci, aktualizaci a zrušení registrace aplikací jsou k dispozici také rozhraní pro hledání a dotazování aplikací pomocí vyhledávacích výrazů. 
- Žádosti o certifikát musí odkazovat na platnou aplikaci, aby bylo možné zpracovat požadavek a vydat podepsaný certifikát se všemi OPC rozšířeními pro UA. 
- Aplikační služba je zajištěna databází v Azure Cosmos DB.

### <a name="certificate-group"></a>Skupina certifikátů
- Skupina certifikátů je entita, která ukládá kořenovou certifikační autoritu nebo certifikát podřízené certifikační autority, včetně privátního klíče pro podepisování certifikátů. 
- Délka klíče RSA, délka hash SHA-2 a životnosti se dají konfigurovat pro certifikační autoritu vystavitele i pro podepsané certifikáty aplikací. 
- Certifikáty certifikační autority v Azure Key Vault uložíte s použitím modulu HARDWAROVÉho zabezpečení FIPS 140-2 úrovně 2. Privátní klíč nikdy neopouští zabezpečené úložiště, protože se k tomu přihlašování používá operace Key Vault zabezpečenou službou Azure AD. 
- Certifikáty certifikační autority můžete v průběhu času prodloužit a jejich udržení v bezpečném úložišti kvůli historii Key Vault. 
- Seznam odvolaných certifikátů pro každý certifikát certifikační autority je uložený také v Key Vault jako tajný klíč. Pokud je aplikace odregistrována, je certifikát aplikace také odvolán v seznamu odvolaných certifikátů (CRL) správcem.
- Můžete odvolat samostatné certifikáty i dávkové certifikáty.

### <a name="certificate-request"></a>Žádost o certifikát
Žádost o certifikát implementuje pracovní postup, aby vygeneroval nový pár klíčů nebo podepsaný certifikát pomocí žádosti o podepsání certifikátu (CSR) pro aplikaci OPC UA. 
- Požadavek je uložen v databázi s doprovodnými informacemi, jako je předmět nebo CSR, a odkazem na aplikaci OPC UA. 
- Obchodní logika ve službě ověřuje požadavek proti informacím uloženým v aplikační databázi. Například identifikátor URI aplikace v databázi se musí shodovat s identifikátorem URI aplikace v CSR.
- Správce zabezpečení s právy pro podpis (tj. role schvalovatele) schválí nebo zamítne žádost. Pokud je žádost schválená, vygenerují se nový pár klíčů nebo podepsaný certifikát (nebo obojí). Nový privátní klíč je bezpečně uložený v Key Vault a nový podepsaný veřejný certifikát je uložený v databázi žádostí o certifikát.
- Žadatel se může dotazovat na stav žádosti, dokud není schválen nebo odvolán. Pokud byl požadavek schválen, privátní klíč a certifikát lze stáhnout a nainstalovat v úložišti certifikátů aplikace OPC UA.
- Žadatel teď může požadavek přijmout, aby odstranil nepotřebné informace z databáze požadavků. 

Po celou dobu životnosti podepsaného certifikátu se může aplikace odstranit nebo může dojít k ohrožení zabezpečení klíče. V takovém případě může správce certifikační autority:
- Odstraní aplikaci, která také odstraní všechny nedokončené a schválené žádosti o certifikát aplikace. 
- Odstraní jenom jednu žádost o certifikát, pokud se jenom obnovuje nebo ohrozí jenom klíč.

Nyní jsou ohrožené schválené a přijaté žádosti o certifikát označeny jako odstraněné.

Správce může pravidelně obnovit seznam odvolaných certifikátů certifikační autority vystavitele. V době obnovení jsou všechny odstraněné žádosti o certifikát odvolány a sériová čísla certifikátů jsou přidána do seznamu odvolaných certifikátů CRL. Odvolané žádosti o certifikát jsou označené jako odvolané. V naléhavých událostech je možné odvolat samostatné žádosti o certifikát.

A nakonec aktualizované seznamy odvolaných certifikátů jsou dostupné pro distribuci do zúčastněných klientů a serverů OPC UA.

## <a name="opc-vault-iot-edge-module"></a>Modul IoT Edge trezoru OPC
Pro podporu globálního serveru zjišťování sítě továrny můžete na hranici nasadit modul OPC trezor. Spusťte ji jako místní aplikaci .NET Core nebo ji spusťte v kontejneru Docker. Vzhledem k tomu, že v aktuálním zásobníku služby OPC UA .NET Standard není podporovaná Podpora ověřování auth2, je funkce modulu Edge trezoru OPC omezená na roli čtenáře. Uživatele nejde zosobnit z modulu Edge ke mikroslužbám pomocí rozhraní OPC UA GDS Standard.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s architekturou trezoru OPC, můžete:

> [!div class="nextstepaction"]
> [Sestavování a nasazování trezoru OPC](howto-opc-vault-deploy.md)
