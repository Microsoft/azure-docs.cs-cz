---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/25/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: db241c1a3c8bfd15e13ae0bd9f1cdf4c92c7081d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "34013932"
---
>[!NOTE]
> Komentáře můžete nechat na této stránce pro zpětnou vazbu nebo prostřednictvím [Azure zpětné vazby](https://feedback.azure.com/forums/216843-virtual-machines) s #azerrormessage značkou.

## <a name="error-response-format"></a>Formát odpovědi chyby 
Virtuální počítače Azure pomocí následujícího formátu JSON pro odpovědi na chybu:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Chybnou odpověď vždy obsahuje stavový kód a objekt chyby. Každý objekt chyba vždy obsahuje kód chyby a zprávu. Pokud virtuální počítač je vytvořen pomocí šablony, objekt chyba obsahuje také podrobnosti oddíl, který obsahuje vnitřní úroveň kódy chyb a zpráv. Za normálních okolností nejvíce vnitřní úroveň chybová zpráva je kořenový selhání.


## <a name="common-virtual-machine-management-errors"></a>Běžné chyby správy virtuálního počítače.

V této části jsou uvedeny běžné chybové zprávy, se můžete setkat při správě virtuálních počítačů:

|  Kód chyby  |  Chybová zpráva  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nepodařilo se získat zapůjčení při vytváření disku '{0}' pomocí identifikátoru URI objektu blob {1}. Objekt blob je již používán.  |  
|  AllocationFailed  |  Přidělení se nezdařilo. Zkuste zmenšit velikost virtuálního počítače nebo počet virtuálních počítačů, opakujte akci později nebo zkuste nasazení na jinou skupinu dostupnosti nebo jiném umístění Azure.  |  
|  AllocationFailed  |  Přidělení virtuálního počítače se nezdařilo z důvodu vnitřní chyby. Opakujte akci později nebo zkuste nasazení do jiného umístění.  |
|  ArtifactNotFound  |  Rozšíření virtuálního počítače vydavatele '{0}"a typ"{1}'nebyl nalezen v umístění'{2}'.  |
|  ArtifactNotFound  |  Rozšíření s vydavatelem '{0}', typu '{1}"a verzí obslužné rutiny typu '{2}' nebyl nalezen v úložišti rozšíření.  |
|  ArtifactVersionNotFound  |  Žádná verze nenašla v úložišti artefaktů, která by splnila požadavek na verzi,{0}'.  |
|  ArtifactVersionNotFound  |  Žádná verze nenašla v úložišti artefaktů, která by splnila požadavek na verzi,{0}'pro rozšíření virtuálního počítače vydavatele'{1}'a typ'{2}'.  |
|  AttachDiskWhileBeingDetached  |  Nelze připojit datový disk se{0}'na virtuální počítač'{1}' vzhledem k tomu, že je disk momentálně odpojuje. Počkejte prosím, než se disk úplně odpojí a akci opakujte.  |
|  BadRequest  |  Zarovnání ' skupiny dostupnosti ještě nejsou v této oblasti nepodporuje.  |
|  BadRequest  |  Přidání virtuálního počítače s disky spravované na nespravované sadu dostupnosti nebo přidání virtuálního počítače s disky na základě objektů blob do spravované skupiny dostupnosti není podporována. Vytvořte skupiny dostupnosti s vlastností 'spravované' nastavit, aby bylo možné přidat virtuální počítač s spravovaných disků na ni.  |
|  BadRequest  |  Spravované disky se v této oblasti nepodporují.  |
|  BadRequest  |  Více rozšíření Vmextension na obslužnou rutinu není podporováno pro operační systém typu '{0}'. Rozšíření VMExtension '{1}'s obslužnou rutinou'{2}' již přidáno nebo určeno ve vstupu.  |
|  BadRequest  |  Operaci '{0}'není podporován u prostředku'{1}' s spravované disky.  |
|  CertificateImproperlyFormatted  |  Reprezentace JSON tajného klíče získaná z {0} obsahuje datové pole, která není správně formátovaným souborem PFX, nebo zadat heslo nedekóduje souboru PFX správně.  |
|  CertificateImproperlyFormatted  |  Data načtená z {0} není deserializovat do formátu JSON.  |
|  Konflikt  |  Změna velikosti disku je povolená jenom při vytvoření virtuálního počítače nebo při zrušení jeho přiřazení.  |
|  ConflictingUserInput  |  Disk se{0}'nelze připojit, protože disk je již vlastněn virtuální počítač'{1}'.  |
|  ConflictingUserInput  |  Zdrojová skupina prostředků je stejná jako cílová skupina prostředků.  |
|  ConflictingUserInput  |  Účty služby storage zdroj a cíl pro disk {0} se liší.  |
|  ContainerAlreadyOnLease  |  V kontejneru úložiště, který uchovává objekt blob se identifikátor URI je již zapůjčení {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Žádost o přesunutí prostředků obsahuje prostředky KeyVault, na které odkazuje jeden nebo více {0}s v požadavku. Aktuálně nejsou podporované mezi předplatnými Move. Zkontrolujte podrobnosti chyby pro identifikátory prostředku KeyVault.  |
|  DiagnosticsOperationInternalError  |  Při zpracovávání diagnostického profilu virtuálního počítače došlo k vnitřní chybě {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Objekt BLOB {0} se již používá jiný disk, který patří k virtuálnímu počítači se{1}'. Můžete zkontrolovat metadata objektu blob pro referenční informace o disku.  |
|  DiskBlobNotFound  |  Nelze najít objekt blob VHD s identifikátorem URI {0} pro disk se{1}'.  |
|  DiskBlobNotFound  |  Nelze najít objekt blob VHD s identifikátorem URI {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} tajný klíč nemá {1} značky. Aktualizovat verzi tajného klíče, přidejte požadované značky a opakujte.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Rozbalení hodnoty tajného klíče {0} hodnotu pomocí klíče {1} se nezdařilo.  |
|  DiskImageNotReady  |  Bitová kopie disku {0} v {1} stavu. Opakujte akci po obrázek je připravený.  |
|  DiskPreparationError  |  Při přípravě disky virtuálních počítačů došlo k jedné nebo více chybám. Najdete v zobrazení instance disku podrobnosti.  |
|  DiskProcessingError  |  Zpracování disku se pozastavilo, protože virtuální počítač má mezi chybovými disky i další disky.  |
|  ImageBlobNotFound  |  Nelze najít objekt blob VHD s identifikátorem URI {0} pro disk se{1}'.  |
|  ImageBlobNotFound  |  Nelze najít objekt blob VHD s identifikátorem URI {0}.  |
|  IncorrectDiskBlobType  |  Diskové objekty BLOB mohou být pouze typu Objekt blob stránky. Objekt BLOB {0} pro disk se{1}' je typu objekt BLOB bloku.  |
|  IncorrectDiskBlobType  |  Diskové objekty BLOB mohou být pouze typu Objekt blob stránky. Objekt BLOB {0} je typu '{1}'.  |
|  IncorrectImageBlobType  |  Diskové objekty BLOB mohou být pouze typu Objekt blob stránky. Objekt BLOB {0} pro disk se{1}' je typu objekt BLOB bloku.  |
|  IncorrectImageBlobType  |  Diskové objekty BLOB mohou být pouze typu Objekt blob stránky. Objekt BLOB {0} je typu '{1}'.  |
|  InternalOperationError  |  Nelze vyřešit účet úložiště {0}. Zkontrolujte, zda že se vytvořil prostřednictvím poskytovatele prostředků úložiště ve stejném umístění jako nachází výpočetní prostředek.  |
|  InternalOperationError  |  {0} úkolů hledání cíle se nezdařilo.  |
|  InternalOperationError  |  Došlo k chybě při ověřování síťového profilu virtuálního počítače se{0}'.  |
|  InvalidAccountType  |  AccountType {0} je neplatný.  |
|  InvalidParameter  |  Hodnota parametru {0} je neplatný.  |
|  InvalidParameter  |  Zadané heslo správce není povolené.  |
|  InvalidParameter  |  "Zadané heslo musí být mezi {0}-{1} znaků a musí splňovat alespoň {2} z požadavky na složitost hesla z následujícího: <ol><li> Obsahuje velké písmeno</li><li>Obsahuje malé písmeno.</li><li>Obsahuje číslici</li><li>Obsahuje speciální znak.</li></ol>  |
|  InvalidParameter  |  Zadané uživatelské jméno správce není povolené.  |
|  InvalidParameter  |  Pokud je virtuální počítač vytvořený z platformy nebo uživatelské image, nejde připojit existující disk s operačním systém.  |
|  InvalidParameter  |  Název kontejneru {0} je neplatný. Názvy kontejnerů musí být 3 až 63 znaků a může obsahovat jenom malé alfanumerické znaky a spojovníky. Pomlčka musí být dlouhé a následuje alfanumerický znak.  |
|  InvalidParameter  |  Název kontejneru {0} v adrese URL {1} je neplatný. Názvy kontejnerů musí být 3 až 63 znaků a může obsahovat jenom malé alfanumerické znaky a spojovníky. Pomlčka musí být dlouhé a následuje alfanumerický znak.  |
|  InvalidParameter  |  Název objektu blob v adrese URL {0} obsahuje lomítko. V současné době není podporována pro disky.  |
|  InvalidParameter  |  Identifikátor URI {0} zdá být správné identifikátor URI objektu blob.  |
|  InvalidParameter  |  Disk s názvem "{0}se už používá stejnou logickou jednotku: {1}.  |
|  InvalidParameter  |  Disk s názvem '{0}' již existuje.  |
|  InvalidParameter  |  Pro disk, který už je v zadané referenci image definovaný, nejde zadat uživatelská přepsání image.  |
|  InvalidParameter  |  Disk s názvem "{0}se už používá stejnou adresu URL VHD {1}.  |
|  InvalidParameter  |  Zadaný počet domén selhání {0} musí spadat do rozsahu {1} k {2}.  |
|  InvalidParameter  |  Typ licence {0} je neplatný. Platné typy licencí jsou: Windows_Client nebo Windows_Server, rozlišují velká a malá písmena.  |
|  InvalidParameter  |  Název hostitele Linux nesmí překročit {0} znaků nebo obsahovat následující znaky: {1}.  |
|  InvalidParameter  |  Cílová cesta pro veřejné klíče Ssh je aktuálně omezená na výchozí hodnotu {0} kvůli známému problému s Linuxovým agentem zřizování.  |
|  InvalidParameter  |  Disk na logické jednotce {0} již existuje.  |
|  InvalidParameter  |  Předplatné {0} požadavku musí shodovat s předplatným {1} obsažené v id spravovaného disku.  |
|  InvalidParameter  |  Vlastní data v OSProfile musí být v kódování Base64 a s maximální délkou {0} znaků.  |
|  InvalidParameter  |  Název objektu BLOB v adrese URL {0} musí končit '{1}' rozšíření.  |
|  InvalidParameter  |  {0}' není platná předpona názvu objektu blob zaznamenané virtuálního pevného disku. Platná předpona odpovídá regulárnímu výrazu '{1}'.  |
|  InvalidParameter  |  Certifikáty nelze přidat k virtuálnímu počítači, pokud není zřízený agent virtuálního počítače.  |
|  InvalidParameter  |  Disk na logické jednotce {0} již existuje.  |
|  InvalidParameter  |  Nelze vytvořit virtuální počítač, protože požadovaná velikost {0} není k dispozici v clusteru, kde je právě přiděleno skupiny dostupnosti. Dostupné velikosti jsou: {1}. Přečtěte si další virtuální počítač na změnu velikosti strategie v https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  Požadovaná velikost virtuálního počítače {0} není k dispozici v aktuální oblasti. Velikosti, která je k dispozici v aktuální oblasti jsou: {1}. Zjistit další informace o dostupných velikostí virtuálních počítačů v každé oblasti v https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Požadovaná velikost virtuálního počítače {0} není k dispozici v aktuální oblasti. Zjistit další informace o dostupných velikostí virtuálních počítačů v každé oblasti v https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Uživatelské jméno správce Windows nemůže být více než {0} znaků, tečkou končit ani obsahovat následující znaky: {1}.  |
|  InvalidParameter  |  Název počítače Windows nemůže být více než {0} znaků, složen výhradně z číslic ani obsahovat následující znaky: {1}.  |
|  MissingMoveDependentResources  |  Žádost o přesun prostředků neobsahuje všechny závislé prostředky. Zkontrolujte podrobnosti o chybě pro ID chybějících prostředků.  |
|  MoveResourcesHaveInvalidState  |  Žádost o přesunutí prostředků obsahuje virtuální počítače, které jsou spojeny s účty úložiště nejsou platné. Zkontrolujte podrobnosti o těchto ID prostředků a názvy účtů odkazovaných úložiště.  |
|  MoveResourcesHavePendingOperations  |  Žádost o přesunutí prostředků obsahuje prostředky, pro které čeká na vyřízení operace. Zkontrolujte podrobnosti o těchto ID prostředků. Opakujte operaci, jakmile budou čekající operace dokončeny.  |
|  MoveResourcesNotFound  |  Žádost o přesunutí prostředků obsahuje prostředky, které nelze nalézt. Zkontrolujte podrobnosti o těchto ID prostředků.  |
|  NetworkingInternalOperationError  |  Neznámá chyba přidělení sítě.  |
|  NetworkingInternalOperationError  |  Neznámá chyba přidělení sítě  |
|  NetworkingInternalOperationError  |  Při zpracování síťového profilu virtuálního počítače nastala vnitřní chyba.  |
|  Nenalezeno  |  Skupina dostupnosti {0} nebyl nalezen.  |
|  Nenalezeno  |  Zdroj virtuálního počítače se{0}' zadaný v požadavku neexistuje v tomto umístění Azure.  |
|  Nenalezeno  |  Klient s id {0} nebyl nalezen.  |
|  Nenalezeno  |  Obrázek {0} nebyl nalezen.  |
|  NotSupported  |  Typ licence je {0}, ale objekt blob image {1} není místní.  |
|  OperationNotAllowed  |  Skupina dostupnosti {0} nelze odstranit. Před odstraněním skupiny dostupnosti zkontrolujte, zda neobsahuje žádné virtuální počítače.  |
|  OperationNotAllowed  |  Změna nastavení dostupnosti SKU z "Pevně" na "Klasickém" není povolena.  |
|  OperationNotAllowed  |  Pokud virtuální počítač neběží, nedají se v něm upravovat rozšíření.  |
|  OperationNotAllowed  |  Akce zaznamenání je podporována pouze na virtuálním počítači s disky, na základě objektů blob. Použijte prosím prostředku 'Image' rozhraní API pro vytvoření Image z spravované virtuálního počítače.  |
|  OperationNotAllowed  |  Prostředek {0} nelze vytvořit z Image {1} dokud bitové kopie byla úspěšně vytvořena.  |
|  OperationNotAllowed  |  Aktualizace nastavení encryptionSettings není povolená, pokud je virtuální počítač přidělen, zkuste to prosím znovu po zrušení jeho přidělení virtuálního počítače  |
|  OperationNotAllowed  |  Přidání spravovaného disku k virtuálnímu počítači s disky, které jsou založené na objektech blob, se nepodporuje.  |
|  OperationNotAllowed  |  Maximální počet datových disků, připojí se k této velikosti virtuálního počítače je {0}.  |
|  OperationNotAllowed  |  Přidání disku, který je založený na objektech blob, k virtuálnímu počítači se spravovanými disky se nepodporuje.  |
|  OperationNotAllowed  |  Operaci '{0}není povolená, na bitovou kopii,{1}, protože bitovou kopii je označena pro odstranění. Můžete pouze operaci odstranění (nebo počkejte některého probíhající Dokončit).  |
|  OperationNotAllowed  |  Operaci '{0}'není povolená na virtuálním počítači,{1}' vzhledem k tomu, že virtuální počítač je zobecněný.  |
|  OperationNotAllowed  |  Operaci '{0}'není povolen jako kolekce bod obnovení'{1}' je označena pro odstranění.  |
|  OperationNotAllowed  |  Operaci '{0}'není povolena u rozšíření virtuálního počítače,{1}' vzhledem k tomu, že je označena pro odstranění. Můžete pouze operaci odstranění (nebo počkejte některého probíhající Dokončit).  |
|  OperationNotAllowed  |  Operaci '{0}není povolená, protože virtuální počítače,{1}'jsou se zřídí pomocí Image'{2}'.  |
|  OperationNotAllowed  |  Operaci '{0}není povolená, protože virtuální počítač ScaleSet'{1}'je aktuálně používá bitovou kopii,{2}'.  |
|  OperationNotAllowed  |  Operaci '{0}'není povolená na virtuálním počítači,{1}se od virtuálního počítače je označena pro odstranění. Můžete pouze operaci odstranění (nebo počkejte některého probíhající Dokončit).  |
|  OperationNotAllowed  |  Operaci '{0}'není povolená na virtuálním počítači,{1}se od virtuálního počítače je zrušeno přiřazení nebo je označený k zrušení přiřazení.  |
|  OperationNotAllowed  |  Operaci '{0}'není povolená na virtuálním počítači,{1}' vzhledem k tomu, že je virtuální počítač spuštěný. Prosím power vypnout explicitně v případě, že vypnout virtuální počítač z uvnitř hostovaného operačního systému.  |
|  OperationNotAllowed  |  Operaci '{0}'není povolená na virtuálním počítači,{1}se od virtuálního počítače není navrácena.  |
|  OperationNotAllowed  |  Operaci '{0}'není povolená na virtuálním počítači,{1}, protože rozšíření má virtuální počítač'{2}' v nezdařilo stavu.  |
|  OperationNotAllowed  |  Operaci '{0}'není povolená na virtuálním počítači,{1}, protože probíhá jiná operace.  |
|  OperationNotAllowed  |  Operaci '{0}, vyžaduje virtuální počítač,{1}' Chcete-li být zobecněn.  |
|  OperationNotAllowed  |  Operace vyžaduje, aby virtuální počítač běžel (nebo aby byl nastaven na spuštění).  |
|  OperationNotAllowed  |  Disk s velikostí {0}GB, což je menší než velikost {1}GB odpovídající disku v bitové kopii, není povolen.  |
|  OperationNotAllowed  |  Škálovací sady virtuálního počítače rozšíření obslužné rutiny '{0}' lze přidat pouze v době vytváření Škálovací sady virtuálních počítačů.  |
|  OperationNotAllowed  |  Škálovací sady virtuálního počítače rozšíření obslužné rutiny,{0}, můžete odstranit jenom během odstraňování Škálovací sady virtuálního počítače.  |
|  OperationNotAllowed  |  Virtuální počítač '{0}se už používá spravované disky.  |
|  OperationNotAllowed  |  Virtuální počítač '{0}, patří do skupiny dostupnosti "Klasickém" '{1}'. Aktualizujte prosím skupinu dostupnosti pomocí 'pevně, SKU a poté opakujte převod.  |
|  OperationNotAllowed  |  Virtuální počítač vytvořený z Image nemůže mít objekt blob na základě disky. Všechny disky musí být spravované disky.  |
|  OperationNotAllowed  |  Operaci zachytávání nejde dokončit, protože virtuální počítač není zobecněný.  |
|  OperationNotAllowed  |  Operace správy na virtuálním počítači se{0}' nejsou povoleny, protože se převedou disky virtuálních počítačů do spravovaných disků.  |
|  OperationNotAllowed  |  Probíhající operace je změna stavu napájení virtuálního počítače {0} k {1}. Proveďte operaci {2} po určité době.  |
|  OperationNotAllowed  |  Nelze přidat nebo aktualizovat virtuální počítač. Požadovaná velikost virtuálního počítače {0} nemusí být k dispozici v existující alokační jednotky. Přečtěte si další virtuální počítač na změnu velikosti strategie v https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nelze změnit velikost virtuálního počítače, protože požadovaná velikost {0} není k dispozici v clusteru, kde je právě přiděleno skupiny dostupnosti. Dostupné velikosti jsou: {1}. Přečtěte si další virtuální počítač na změnu velikosti strategie v https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nelze změnit velikost virtuálního počítače, protože požadovaná velikost {0} není k dispozici v clusteru, kde je aktuálně přidělené virtuální počítač. Ke změně velikosti virtuálního počítače do {1} prosím navrácení (to je zastavení operace na portálu Azure) a změny velikosti operaci opakujte. Přečtěte si další virtuální počítač na změnu velikosti strategie v https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Zřizování operačního systému se nezdařilo pro virtuální počítač '{0}, protože se právě zřizuje hostovaný operační systém.  |
|  OSProvisioningClientError  |  Zřizování operačního systému pro virtuální počítač '{0}' se nezdařilo. Podrobnosti o chybě: {1} zajistěte, aby image správně připravena (generalizována). <ul><li>Pokyny pro Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Generování klíčů SSH hostitele se nezdařilo. Podrobnosti o chybě: {0}. Chcete-li vyřešit tento problém ověřit, pokud je správně nastavený agenta systému Linux. <ul><li>Můžete zkontrolovat, postupujte podle pokynů v: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/ </li></ul> |
|  OSProvisioningClientError  |  Uživatelské jméno pro virtuální počítač je neplatný pro této distribuce systému Linux. Podrobnosti o chybě: {0}.  |
|  OSProvisioningInternalError  |  Zřizování operačního systému se nezdařilo pro virtuální počítač '{0}se kvůli vnitřní chybě.  |
|  OSProvisioningTimedOut  |  Zřizování operačního systému pro virtuální počítač '{0}' nebyl dokončen v přiděleném čase. Virtuální počítač může stále dokončit úspěšně. Zkontrolujte prosím stav zřizování později.  |
|  OSProvisioningTimedOut  |  Zřizování operačního systému pro virtuální počítač '{0}' nebyl dokončen v přiděleném čase. Virtuální počítač může stále dokončit úspěšně. Zkontrolujte prosím stav zřizování později. Taky se ujistěte, image správně připravena (generalizována).   <ul><li>Pokyny pro Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Pokyny pro Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Zřizování operačního systému pro virtuální počítač '{0}' nebyl dokončen v přiděleném čase. Agenta hosta virtuálního počítače, ale byla zjištěna systémem. To naznačuje, že hostovaný operační systém nebyl správně připravené pro použití jako image virtuálního počítače (CreateOption = FromImage). Chcete-li vyřešit tento problém, buď použijte virtuální pevný disk, protože je s CreateOption = připojit nebo ji správně připravit pro použití jako obrázek:   <ul><li>Pokyny pro Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Pokyny pro Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Požadovaná velikost virtuálního počítače není v tuto chvíli na vybraném umístění dostupná.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Prostředek nelze aktualizovat, protože aktualizace probíhající platformy. Zkuste to prosím znova později.  |
|  StorageAccountLimitation  |  Účet úložiště se{0}se nepodporuje objekty BLOB stránky, které se vyžadují k vytváření disků.  |
|  StorageAccountLimitation  |  Účet úložiště se{0}' překročil jemu přidělenou kvótu.  |
|  StorageAccountLocationMismatch  |  Nelze vyřešit účet úložiště {0}. Zkontrolujte, zda že se vytvořil prostřednictvím poskytovatele prostředků úložiště ve stejném umístění jako nachází výpočetní prostředek.  |
|  StorageAccountNotFound  |  Účet úložiště {0} nebyl nalezen. Zkontrolujte účet úložiště není odstraněný a že patří do stejného umístění Azure jako virtuální počítač.  |
|  StorageAccountNotRecognized  |  Použijte prosím účet úložiště spravovaný poskytovatelem prostředků úložiště. Použití {0} není podporován.  |
|  StorageAccountOperationInternalError  |  Při přístupu k účtu úložiště došlo k vnitřní chybě {0}.  |
|  StorageAccountSubscriptionMismatch  |  Účet úložiště {0} nenáleží do předplatného {1}.  |
|  StorageAccountTooBusy  |  Účet úložiště se{0}' je aktuálně zaneprázdněna. Zvažte použití jiného účtu.  |
|  StorageAccountTypeNotSupported  |  Disk {0} používá {1} což je účet úložiště Blob. Zkuste to prosím znovu s účtem úložiště pro obecné účely.  |
|  StorageAccountTypeNotSupported  |  Účet úložiště {0} je {1} typu. Spouštění diagnostiky podporuje {2} typy účtů úložiště.  <ul><li>K této chybě dojde, pokud používáte účet úložiště premium pro Diagnostika spouštění. Další informace najdete v tématu [použití Diagnostika spouštění](../articles/virtual-machines/windows/boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Předplatné není autorizováno.  |
|  TargetDiskBlobAlreadyExists  |  Objekt BLOB {0} již existuje. Zadejte jiný identifikátor URI vytvořit nový prázdný datový disk objektu blob se{1}'.  |
|  TargetDiskBlobAlreadyExists  |  Zaznamenat operace nemůže pokračovat, protože cílový objekt blob image {0} již existuje a není nastavený příznak k přepsání objektů BLOB VHD. Objekt blob odstraňte nebo nastavte příznak k přepsání objektů BLOB virtuálního pevného disku a opakujte.  |
|  TargetDiskBlobAlreadyExists  |  Zaznamenat operace nemůže pokračovat, protože cílový objekt blob image {0} má aktivní zapůjčení na.   |
|  TargetDiskBlobAlreadyExists  |  Objekt BLOB {0} již existuje. Zadejte jiný identifikátor URI objektu blob jako cíl pro disk se{1}'.  |
|  TooManyVMRedeploymentRequests  |  Obdrželi příliš mnoho požadavků na opětovné nasazení virtuálního počítače se{0}, nebo virtuální počítače ve stejné sadě dostupnosti tohoto virtuálního počítače. Zkuste to prosím znovu později.  |
|  VHDSizeInvalid  |  Zadaná hodnota velikosti disku z {0} pro disk se{1}' s objektem blob {2} je neplatný. Velikost disku musí být mezi {3} a {4}.  |
|  VMAgentStatusCommunicationError  |  Virtuální počítač '{0}' neohlásil stav pro agenta virtuálního počítače nebo rozšíření. Zkontrolujte, zda virtuální počítač je spuštěn agent virtuálního počítače a možné navázat odchozí připojení k úložišti Azure.  |
|  VMArtifactRepositoryInternalError  |  Při komunikaci s úložištěm artefaktů, aby se získaly podrobnosti o artefaktu virtuálního počítače, nastala chyba.  |
|  VMArtifactRepositoryInternalError  |  Při načítání dat artefaktů virtuálního počítače z úložiště artefaktů došlo k vnitřní chybě.  |
|  VMExtensionHandlerNonTransientError  |  Obslužná rutina se{0}"oznámil chybu pro rozšíření virtuálního počítače,{1}'s kódem chyby terminálu,{2}' a chybová zpráva: '{3}'  |
|  VMExtensionManagementInternalError  |  Došlo k vnitřní chybě při zpracování rozšíření virtuálního počítače se{0}'.  |
|  VMExtensionManagementInternalError  |  Při přípravě rozšíření virtuálního počítače došlo k několika chybám. Najdete v zobrazení instance virtuálního počítače rozšíření podrobnosti.  |
|  VMExtensionProvisioningError  |  Virtuální počítač nahlásil chybu při zpracování rozšíření '{0}'. Chybová zpráva: "{1}".  |
|  VMExtensionProvisioningError  |  Několik rozšíření virtuálního počítače se nepovedlo zřídit ve virtuálním počítači. Podrobnosti viz zobrazení instance virtuálního počítače rozšíření podrobnosti.  |
|  VMExtensionProvisioningTimeout  |  Zřizování rozšíření virtuálního počítače se{0}' vypršel. Instalace rozšíření možná trvá příliš dlouho, nebo nebylo možné získat stav rozšíření.  |
|  VMMarketplaceInvalidInput  |  Vytvoření virtuálního počítače z image pořízenou prostřednictvím Marketplace bez nemusí informace o plánu, odeberte prosím informace o plánu v požadavku. Název disku operačního systému je {0}.  |
|  VMMarketplaceInvalidInput  |  Informace o nákupu neodpovídá. Nelze nasadit z webu Marketplace bitové kopie. Název disku operačního systému je {0}.  |
|  VMMarketplaceInvalidInput  |  Vytvoření virtuálního počítače image pořízené na Marketplace vyžaduje informace o plánu v požadavku. Název disku operačního systému je {0}.  |
|  VMNotFound  |  Virtuální počítač '{0}' nebyl nalezen.  |
|  VMRedeploymentFailed  |  Virtuální počítač '{0}' nové nasazení se nezdařilo z důvodu vnitřní chyby. Zkuste to prosím znovu později.  |
|  VMRedeploymentTimedOut  |  Opětovné nasazení virtuálního počítače se{0}' neskončila v přiděleném čase. Se může úspěšně dokončit v určité době. Jinak můžete tento požadavek zopakovat.  |
|  VMStartTimedOut  |  Virtuální počítač '{0}' nebyla spuštěna v přiděleném čase. Virtuální počítač stále může úspěšně spustit. Zkontrolujte prosím stav napájení později.  |


## <a name="next-steps"></a>Další postup
Pokud potřebujete další pomoc, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.