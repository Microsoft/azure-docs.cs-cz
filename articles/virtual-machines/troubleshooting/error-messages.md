---
title: Běžné kódy chyb virtuálních počítačů v Azure | Dokumentace Microsoftu
description: Správně chápat některé běžné kódy chyb při zřizovat a spravovat virtuální počítače v Azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: 80b99880362c3979ae5ead6d1cdff4091d23d065
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118971"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Vysvětlení běžných chybových zpráv při správě virtuálních počítačů v Azure

Tento článek popisuje některé z nejběžnějších kódy chyb a zprávy, které mohou nastat, když budete vytvářet a spravovat virtuální počítače (VM) v Azure.

>[!NOTE]
> Můžete ponechat komentáře na této stránce pro zpětnou vazbu nebo prostřednictvím [váš názor na Azure](https://feedback.azure.com/forums/216843-virtual-machines) #azerrormessage značkou.

## <a name="error-response-format"></a>Chyba formátu odpovědi 
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

Reakce na chybu obsahuje vždy stavový kód a objekt error. Každý objekt chyba vždy obsahuje chybový kód a napište zprávu. Pokud virtuální počítač je vytvořen pomocí šablony, objekt error také obsahuje podrobnosti o oddíl, který obsahuje vnitřní úroveň kódy chyb a zprávy. Za normálních okolností nejvíce vnitřní úroveň chybová zpráva je kořenový selhání.


## <a name="common-virtual-machine-management-errors"></a>Běžné chyby správy virtuálního počítače

Tato část uvádí běžné chybové zprávy, se můžete setkat při správě virtuálních počítačů:

|  Kód chyby  |  Chybová zpráva  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nepovedlo se získat zapůjčení při vytváření disku "{0}" pomocí objektů blob s identifikátorem URI {1}. Objekt BLOB se už používá.  |  
|  AllocationFailed  |  Přidělení selhalo. Zkuste zmenšit velikost virtuálního počítače nebo počet virtuálních počítačů, zkuste to znovu později nebo zkuste nasazení provést jinou skupinu dostupnosti nebo jiného umístění Azure.  |  
|  AllocationFailed  |  Přidělení virtuálního počítače se nezdařilo z důvodu vnitřní chyby. Zkuste to znovu později nebo zkuste nasazení provést do jiného umístění.  |
|  ArtifactNotFound  |  Rozšíření virtuálního počítače o vydavatele "{0}"a typ"{1}'nebyla nalezena v umístění'{2}".  |
|  ArtifactNotFound  |  Rozšíření s vydavatelem "{0}', typ"{1}"a verzí obslužné rutiny typu"{2}' nebyl nalezen v úložišti rozšíření.  |
|  ArtifactVersionNotFound  |  Se nenašla žádná verze v úložišti artefaktů, který splňuje požadovanou verzi "{0}".  |
|  ArtifactVersionNotFound  |  Se nenašla žádná verze v úložišti artefaktů, který splňuje požadovanou verzi "{0}"pro rozšíření virtuálního počítače o vydavatele"{1}"a typ"{2}".  |
|  AttachDiskWhileBeingDetached  |  Nejde připojit datový disk "{0}"k virtuálnímu počítači"{1}' protože disk je v tuto chvíli odpojuje. Počkejte prosím, než se disk úplně odpojí a pak to zkuste znovu.  |
|  BadRequest  |  Zarovnání ' skupiny dostupnosti v této oblasti zatím nepodporují.  |
|  BadRequest  |  Přidání virtuálního počítače se spravovanými disky do nespravované skupiny dostupnosti nebo přidání virtuálního počítače s disky založenými na objektech blob na spravované skupiny dostupnosti se nepodporuje. Vytvořte prosím skupinu dostupnosti s nastavenou chcete přidat virtuální počítač se spravovanými disky do ní vlastnost "spravované".  |
|  BadRequest  |  Spravované disky se v této oblasti nepodporují.  |
|  BadRequest  |  Více rozšíření Vmextension na obslužnou rutinu nejsou podporovány pro typ operačního systému "{0}". Rozšíření VMExtension "{1}"s obslužnou rutinou"{2}" již přidáno nebo určeno ve vstupu.  |
|  BadRequest  |  Operace "{0}"není podporován u prostředku"{1}" se spravovanými disky.  |
|  CertificateImproperlyFormatted  |  Reprezentace JSON tajného klíče načten z {0} obsahuje datové pole, která není správně formátovaným souborem PFX, nebo si poskytnuté heslo nedekóduje soubor PFX správně.  |
|  CertificateImproperlyFormatted  |  Data načtená z {0} není deserializovat do formátu JSON.  |
|  Konflikt  |  Změna velikosti disku je povolená jenom při vytvoření virtuálního počítače nebo při zrušení jeho přiřazení.  |
|  ConflictingUserInput  |  Disk "{0}"nejde připojit, protože disk je již vlastněn virtuálního počítače"{1}".  |
|  ConflictingUserInput  |  Zdrojová skupina prostředků je stejná jako cílová skupina prostředků.  |
|  ConflictingUserInput  |  Účty zdrojového a cílového úložiště pro disk {0} se liší.  |
|  ContainerAlreadyOnLease  |  Již existuje zapůjčení na kontejner úložiště, který uchovává objekt blob s identifikátorem URI {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Požadavek na přesun prostředků obsahuje prostředky KeyVault, na které odkazuje jeden nebo více {0}s v požadavku. To není podporováno v současné době mezi předplatnými přesunout. ID prostředku KeyVault najdete v podrobnostech chyby.  |
|  DiagnosticsOperationInternalError  |  Při zpracovávání diagnostického profilu virtuálního počítače došlo k vnitřní chybě {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Objekt BLOB {0} se už používá jiný disk, který patří k virtuálnímu počítači "{1}". Můžete prozkoumat metadata objektu blob pro referenční informace o disku.  |
|  DiskBlobNotFound  |  Nepovedlo se najít objekt blob VHD s identifikátorem URI {0} pro disk "{1}".  |
|  DiskBlobNotFound  |  Nepovedlo se najít objekt blob VHD s identifikátorem URI {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} tajný klíč nemá {1} značky. Aktualizujte prosím verzi tajného klíče, přidejte požadované značky a zkuste to znovu.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Rozbalení tajného klíče {0} hodnotu pomocí klíče {1} se nezdařilo.  |
|  DiskImageNotReady  |  Image disku {0} probíhá {1} stavu. Zkuste to prosím znovu po bitová kopie připravena.  |
|  DiskPreparationError  |  Během přípravy disků virtuálního počítače došlo k jedné nebo více chybám. Zobrazit zobrazení instance disků pro podrobnosti.  |
|  DiskProcessingError  |  Zpracování disku se pozastavilo, protože virtuální počítač má mezi chybovými disky i další disky.  |
|  ImageBlobNotFound  |  Nepovedlo se najít objekt blob VHD s identifikátorem URI {0} pro disk "{1}".  |
|  ImageBlobNotFound  |  Nepovedlo se najít objekt blob VHD s identifikátorem URI {0}.  |
|  IncorrectDiskBlobType  |  Objekty BLOB disků můžou být jenom typu Objekt blob stránky. Objekt BLOB {0} pro disk "{1}" je typu objekt BLOB bloku.  |
|  IncorrectDiskBlobType  |  Objekty BLOB disků můžou být jenom typu Objekt blob stránky. Objekt BLOB {0} je typu '{1}".  |
|  IncorrectImageBlobType  |  Objekty BLOB disků můžou být jenom typu Objekt blob stránky. Objekt BLOB {0} pro disk "{1}" je typu objekt BLOB bloku.  |
|  IncorrectImageBlobType  |  Objekty BLOB disků můžou být jenom typu Objekt blob stránky. Objekt BLOB {0} je typu '{1}".  |
|  InternalOperationError  |  Nepovedlo se vyhodnotit účet úložiště {0}. Ujistěte se prosím, že se vytvořil prostřednictvím poskytovatele prostředků úložiště ve stejném umístění jako výpočetních prostředků.  |
|  InternalOperationError  |  {0} úkolů hledání cíle se nezdařilo.  |
|  InternalOperationError  |  Došlo k chybě při ověřování síťového profilu virtuálního počítače "{0}".  |
|  InvalidAccountType  |  AccountType {0} je neplatný.  |
|  InvalidParameter  |  Hodnota parametru {0} je neplatný.  |
|  InvalidParameter  |  Zadané heslo správce není povolené.  |
|  InvalidParameter  |  "Zadané heslo musí být mezi {0}-{1} znaků dlouhé a musí splňovat aspoň {2} z požadavky na složitost hesla z následujících možností: <ol><li> Obsahuje velké písmeno</li><li>Obsahuje malé písmeno</li><li>Obsahuje numerickou číslici</li><li>Obsahuje speciální znak.</li></ol>  |
|  InvalidParameter  |  Zadané uživatelské jméno správce není povolené.  |
|  InvalidParameter  |  Pokud je virtuální počítač vytvořený z platformy nebo uživatelské image, nejde připojit existující disk s operačním systém.  |
|  InvalidParameter  |  Název kontejneru {0} je neplatný. Názvy kontejnerů musí být dlouhý 3 až 63 znaků a může obsahovat jenom malé alfanumerické znaky a pomlčky. Spojovníkem musí být dlouhé a za nímž následuje alfanumerický znak.  |
|  InvalidParameter  |  Název kontejneru {0} v adrese URL {1} je neplatný. Názvy kontejnerů musí být dlouhý 3 až 63 znaků a může obsahovat jenom malé alfanumerické znaky a pomlčky. Spojovníkem musí být dlouhé a za nímž následuje alfanumerický znak.  |
|  InvalidParameter  |  Název objektu blob v adrese URL {0} obsahuje lomítko. V současné době se nepodporuje pro disky.  |
|  InvalidParameter  |  Identifikátor URI {0} vzhled není správný identifikátor URI objektu blob.  |
|  InvalidParameter  |  Disk s názvem "{0}se už používá stejnou logickou jednotku: {1}.  |
|  InvalidParameter  |  Disk s názvem "{0}' již existuje.  |
|  InvalidParameter  |  Pro disk, který už je v zadané referenci image definovaný, nejde zadat uživatelská přepsání image.  |
|  InvalidParameter  |  Disk s názvem "{0}se už používá stejnou adresu URL virtuálního pevného disku {1}.  |
|  InvalidParameter  |  Počet domén selhání zadané {0} musí spadat do rozsahu {1} k {2}.  |
|  InvalidParameter  |  Typ licence {0} je neplatný. Platné typy licencí jsou: Windows_Client nebo Windows_Server, velká a malá písmena.  |
|  InvalidParameter  |  Názvu hostitele Linuxu nemůže být delší než {0} znaků nebo obsahovat následující znaky: {1}.  |
|  InvalidParameter  |  Cílová cesta pro veřejné klíče Ssh je aktuálně omezená na výchozí hodnotu {0} kvůli známému problému s Linuxovým agentem zřizování.  |
|  InvalidParameter  |  Disk na logické jednotce {0} již existuje.  |
|  InvalidParameter  |  Předplatné {0} požadavku musí shodovat s předplatným {1} obsažené v id spravovaného disku.  |
|  InvalidParameter  |  Vlastní data v OSProfile musí být v kódování Base64 a s maximální délkou {0} znaků.  |
|  InvalidParameter  |  Název objektu BLOB v adrese URL {0} musí končit '{1}"rozšíření.  |
|  InvalidParameter  |  {0}"není platná předpona názvu objektu blob zachycené virtuálního pevného disku. Platná předpona odpovídá regulárnímu "{1}".  |
|  InvalidParameter  |  Certifikáty nelze přidat k vašemu virtuálnímu počítači, pokud není zřízený agent virtuálního počítače.  |
|  InvalidParameter  |  Disk na logické jednotce {0} již existuje.  |
|  InvalidParameter  |  Nepovedlo se vytvořit virtuální počítač, protože požadovaná velikost {0} není k dispozici v clusteru, kde je právě přiděleno skupinu dostupnosti. Dostupné velikosti jsou: {1}. Přečtěte si informace o virtuálního počítače na strategii změny velikosti https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  Požadovaná velikost virtuálního počítače {0} není v aktuální oblasti k dispozici. Velikosti k dispozici v aktuální oblasti jsou: {1}. Přečtěte si další informace o dostupných velikostech virtuálních počítačů v jednotlivých oblastech najdete na https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Požadovaná velikost virtuálního počítače {0} není v aktuální oblasti k dispozici. Přečtěte si další informace o dostupných velikostech virtuálních počítačů v jednotlivých oblastech najdete na https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Uživatelské jméno správce Windows nemůže být více než {0} znaků dlouhé, končit tečku (.) nebo obsahovat následující znaky: {1}.  |
|  InvalidParameter  |  Název počítače Windows nemůže být více než {0} znaků dlouhé, obsahovat jenom čísla nebo obsahovat následující znaky: {1}.  |
|  MissingMoveDependentResources  |  Požadavek na přesun prostředků neobsahuje všechny závislé prostředky. Zkontrolujte prosím podrobnosti o chybě zjistili ID chybějících prostředků.  |
|  MoveResourcesHaveInvalidState  |  Požadavek na přesun prostředků obsahuje virtuální počítače, které jsou spojené s účty úložiště nejsou platné. Zkontrolujte prosím podrobnosti těchto ID prostředků a názvy účtů odkazovaných úložiště.  |
|  MoveResourcesHavePendingOperations  |  Požadavek na přesun prostředků obsahuje prostředky, které operace čeká na vyřízení. Zkontrolujte prosím podrobnosti těchto ID prostředků. Zopakujte operaci, jakmile budou čekající operace dokončeny.  |
|  MoveResourcesNotFound  |  Požadavek na přesun prostředků obsahuje prostředky, které se nenašel. Zkontrolujte prosím podrobnosti těchto ID prostředků.  |
|  NetworkingInternalOperationError  |  Neznámá chyba přidělení sítě.  |
|  NetworkingInternalOperationError  |  Neznámá chyba přidělení sítě  |
|  NetworkingInternalOperationError  |  Při zpracování síťového profilu virtuálního počítače nastala vnitřní chyba.  |
|  NotFound  |  Skupina dostupnosti {0} nebyl nalezen.  |
|  NotFound  |  Zdrojový virtuální počítač "{0}' zadaný v požadavku v tomto umístění Azure neexistuje.  |
|  NotFound  |  Klient s id {0} nebyl nalezen.  |
|  NotFound  |  Na obrázku {0} nebyl nalezen.  |
|  NotSupported  |  Typ licence je {0}, ale objekt blob image {1} není místní.  |
|  OperationNotAllowed  |  Skupina dostupnosti {0} nelze odstranit. Před odstraněním skupiny dostupnosti Ujistěte se, že neobsahuje žádné virtuální počítač.  |
|  OperationNotAllowed  |  Změna skupiny dostupnosti skladové položky z "Pevně" k "Klasickém" není povolena.  |
|  OperationNotAllowed  |  Pokud virtuální počítač neběží, nedají se v něm upravovat rozšíření.  |
|  OperationNotAllowed  |  Akce zachytávání se podporuje jenom na virtuálním počítači s disky založenými na objektech blob. Použijte prosím API prostředku "Image" Pokud chcete vytvořit Image ze spravovaného virtuálního počítače.  |
|  OperationNotAllowed  |  Prostředek {0} nelze vytvořit z Image {1} dokud Image byla úspěšně vytvořena.  |
|  OperationNotAllowed  |  Aktualizace encryptionSettings není povolený, pokud je přidělena virtuálnímu počítači, zkuste to prosím znovu po zrušení přidělení virtuálního počítače  |
|  OperationNotAllowed  |  Přidání spravovaného disku k virtuálnímu počítači s disky, které jsou založené na objektech blob, se nepodporuje.  |
|  OperationNotAllowed  |  Maximální počet datových disků můžou být připojené k virtuálnímu počítači této velikosti se {0}.  |
|  OperationNotAllowed  |  Přidání disku, který je založený na objektech blob, k virtuálnímu počítači se spravovanými disky se nepodporuje.  |
|  OperationNotAllowed  |  Operace "{0}'není povolena v bitové kopii'{1}' protože Image je označená k odstranění. Můžete pouze operaci odstranění zopakovat (nebo počkat se ta probíhající dokončí).  |
|  OperationNotAllowed  |  Operace "{0}"není povolena na virtuálním počítači"{1}' protože tento virtuální počítač je zobecněný.  |
|  OperationNotAllowed  |  Operace "{0}"není povolen jako kolekci bodů obnovení"{1}" je označená k odstranění.  |
|  OperationNotAllowed  |  Operace "{0}"není povolený u rozšíření virtuálního počítače"{1}' protože je označený k odstranění. Můžete pouze operaci odstranění zopakovat (nebo počkat se ta probíhající dokončí).  |
|  OperationNotAllowed  |  Operace "{0}"není povolená, protože služba Virtual Machines "{1}"se zřizuje pomocí Image"{2}".  |
|  OperationNotAllowed  |  Operace "{0}"není povolená, protože škálovací sadě virtuálních počítačů"{1}'je aktuálně pomocí bitové kopie'{2}".  |
|  OperationNotAllowed  |  Operace "{0}"není povolena na virtuálním počítači"{1}' protože virtuální počítač je označený k odstranění. Můžete pouze operaci odstranění zopakovat (nebo počkat se ta probíhající dokončí).  |
|  OperationNotAllowed  |  Operace "{0}"není povolena na virtuálním počítači"{1}' protože tento virtuální počítač uvolněný nebo označený k zrušení přiřazení.  |
|  OperationNotAllowed  |  Operace "{0}"není povolena na virtuálním počítači"{1}' vzhledem k tomu, že virtuální počítač běží. Prosím power vypnout explicitně v případě, že vypnout virtuální počítač z uvnitř hostovaného operačního systému.  |
|  OperationNotAllowed  |  Operace "{0}"není povolena na virtuálním počítači"{1}' protože virtuální počítač není uvolněný.  |
|  OperationNotAllowed  |  Operace "{0}"není povolena na virtuálním počítači"{1}'protože virtuální počítač má rozšíření"{2}' v chybovém stavu.  |
|  OperationNotAllowed  |  Operace "{0}"není povolena na virtuálním počítači"{1}' protože probíhá jiná operace.  |
|  OperationNotAllowed  |  Operaci '{0}"vyžaduje, aby virtuální počítač"{1}' byl zobecněný.  |
|  OperationNotAllowed  |  Operace vyžaduje, aby virtuální počítač běžel (nebo aby byl nastaven na spuštění).  |
|  OperationNotAllowed  |  Disk o velikosti {0}GB, která je menší než velikost {1}GB odpovídajícího disku ve vlastnosti Image, není povolený.  |
|  OperationNotAllowed  |  Škálovací sady virtuálního počítače rozšíření obslužné rutiny "{0}' lze přidat pouze v době vytvoření Škálovací sady virtuálních počítačů.  |
|  OperationNotAllowed  |  Škálovací sady virtuálního počítače rozšíření obslužné rutiny "{0}' můžete odstranit jenom během odstraňování Škálovací sady virtuálních počítačů.  |
|  OperationNotAllowed  |  Virtuální počítač "{0}' se už používá spravované disky.  |
|  OperationNotAllowed  |  Virtuální počítač "{0}"patří do skupiny dostupnosti "Klasickém" "{1}". Aktualizujte prosím dostupnosti používat "Pevně" skladové položky a zkuste převod zopakovat.  |
|  OperationNotAllowed  |  Virtuální počítač vytvořený z Image nemůže mít disky založené na objektech blob. Všechny disky musí být spravované disky.  |
|  OperationNotAllowed  |  Operaci zachytávání nejde dokončit, protože virtuální počítač není zobecněný.  |
|  OperationNotAllowed  |  Operace správy virtuálního počítače "{0}" nejsou povoleny, protože disky virtuálního počítače se převádějí na spravované disky.  |
|  OperationNotAllowed  |  Probíhající operace mění stav napájení virtuálního počítače {0} k {1}. Proveďte operaci {2} po nějaké době.  |
|  OperationNotAllowed  |  Nelze přidat nebo aktualizovat virtuální počítač. Požadovaná velikost virtuálního počítače {0} nemusí být k dispozici v existující alokační jednotky. Přečtěte si informace o virtuálního počítače na strategii změny velikosti https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nelze změnit velikost virtuálního počítače, protože požadovaná velikost {0} není k dispozici v clusteru, kde je právě přiděleno skupinu dostupnosti. Dostupné velikosti jsou: {1}. Přečtěte si informace o virtuálního počítače na strategii změny velikosti https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nelze změnit velikost virtuálního počítače, protože požadovaná velikost {0} není k dispozici v clusteru, kde je virtuální počítač právě přiděleno. Změna velikosti virtuálního počítače, aby {1} zopakovat (Toto je operace zastavit na webu Azure Portal) a opakujte operaci změny velikosti. Přečtěte si informace o virtuálního počítače na strategii změny velikosti https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Zřizování operačního systému se nepodařilo pro virtuální počítač "{0}' vzhledem k tomu, že hostovaný operační systém je v tuto chvíli zřizuje.  |
|  OSProvisioningClientError  |  Zřizování operačního systému pro virtuální počítač "{0}' se nezdařilo. Podrobnosti o chybě: {1} Ujistěte se, že image správně připravila (generalizovala). <ul><li>Pokyny pro Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Nepovedlo se vygenerovat klíč hostitele SSH. Podrobnosti o chybě: {0}. Chcete-li vyřešit tento problém ověřit, pokud je správně nastavený agenta pro Linux. <ul><li>Postupujte podle pokynů v, můžete zkontrolovat: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Uživatelské jméno zadané pro virtuální počítač je neplatný pro tuto distribuci Linuxu. Podrobnosti o chybě: {0}.  |
|  OSProvisioningInternalError  |  Zřizování operačního systému se nepodařilo pro virtuální počítač "{0}' z důvodu vnitřní chyby.  |
|  OSProvisioningTimedOut  |  Zřizování operačního systému pro virtuální počítač "{0}" nebylo dokončeno v přiděleném čase. Virtuální počítač může dokončit úspěšně. Zkontrolujte prosím stav zřizování později.  |
|  OSProvisioningTimedOut  |  Zřizování operačního systému pro virtuální počítač "{0}" nebylo dokončeno v přiděleném čase. Virtuální počítač může dokončit úspěšně. Zkontrolujte prosím stav zřizování později. Také se ujistěte, že image správně připravila (generalizovala).   <ul><li>Pokyny pro Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Pokyny pro Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Zřizování operačního systému pro virtuální počítač "{0}" nebylo dokončeno v přiděleném čase. Agent hosta virtuálního počítače, ale byla zjištěna systémem. To naznačuje, že hostovaný operační systém nebyl správně připravený k použití jako image virtuálního počítače (pomocí CreateOption = FromImage). Chcete-li tento problém vyřešit, použijte virtuální pevný disk, jak je s CreateOption = Attach nebo správně připravený k použití jako image:   <ul><li>Pokyny pro Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Pokyny pro Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Požadovaná velikost virtuálního počítače není aktuálně k dispozici ve vybraném umístění.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Prostředek nelze aktualizovat v tuto chvíli z důvodu probíhající platform update. Zkuste to prosím znova později.  |
|  StorageAccountLimitation  |  Účet úložiště '{0}"nepodporuje objekty BLOB stránky, které jsou nutné k vytváření disků.  |
|  StorageAccountLimitation  |  Účet úložiště '{0}"překročil jemu přidělenou kvótu.  |
|  StorageAccountLocationMismatch  |  Nepovedlo se vyhodnotit účet úložiště {0}. Ujistěte se prosím, že se vytvořil prostřednictvím poskytovatele prostředků úložiště ve stejném umístění jako výpočetních prostředků.  |
|  StorageAccountNotFound  |  Účet úložiště {0} nebyl nalezen. Ujistěte se účet úložiště neodstranil a že patří do stejného umístění Azure jako virtuální počítač.  |
|  StorageAccountNotRecognized  |  Použijte prosím účet úložiště spravované přes poskytovatele prostředků úložiště. Použití {0} se nepodporuje.  |
|  StorageAccountOperationInternalError  |  Při přístupu k účtu úložiště došlo k vnitřní chybě {0}.  |
|  StorageAccountSubscriptionMismatch  |  Účet úložiště {0} nepatří do předplatného {1}.  |
|  StorageAccountTooBusy  |  Účet úložiště '{0}' je aktuálně zaneprázdněna. Zvažte použití jiného účtu.  |
|  StorageAccountTypeNotSupported  |  Disk {0} používá {1} což je účet Blob storage. Zkuste to prosím znovu s účtem úložiště pro obecné účely.  |
|  StorageAccountTypeNotSupported  |  Účet úložiště {0} je {1} typu. Spuštění diagnostiky podporuje {2} typech účtů úložiště.  <ul><li>K této chybě dochází, pokud používáte účet premium storage pro diagnostiku spouštění. Další informace najdete v tématu [jak používat diagnostiku spouštění](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Předplatné není autorizováno.  |
|  TargetDiskBlobAlreadyExists  |  Objekt BLOB {0} již existuje. Zadejte jiný identifikátor URI, chcete-li vytvořit nový prázdný datový disk objektu blob "{1}".  |
|  TargetDiskBlobAlreadyExists  |  Zachycení operace nemůže pokračovat, protože image cílové objektů blob {0} již existuje a není nastaven příznak k přepsání objektů BLOB VHD. Objekt blob odstraňte nebo nastavte příznak k přepsání objektů BLOB VHD a zkuste to znovu.  |
|  TargetDiskBlobAlreadyExists  |  Zachycení operace nemůže pokračovat, protože image cílové objektů blob {0} má aktivní zapůjčení na něj.   |
|  TargetDiskBlobAlreadyExists  |  Objekt BLOB {0} již existuje. Zadejte jiný identifikátor URI objektu blob jako cíl pro disk "{1}".  |
|  TooManyVMRedeploymentRequests  |  Byly přijaty příliš mnoho požadavků na opětovné nasazení pro virtuální počítač "{0}" nebo virtuální počítače ve stejné sadě dostupnosti tohoto virtuálního počítače. Zkuste to prosím znovu později.  |
|  VHDSizeInvalid  |  Hodnota velikosti disku zadané {0} pro disk "{1}" s objektem blob {2} je neplatný. Velikost disku musí být mezi {3} a {4}.  |
|  VMAgentStatusCommunicationError  |  Virtuální počítač "{0}" neohlásil stav agenta virtuálního počítače nebo rozšíření. Zkontrolujte, zda virtuální počítač má spuštěn agent virtuálního počítače a můžete navázat odchozí připojení ke službě Azure storage.  |
|  VMArtifactRepositoryInternalError  |  Při komunikaci s úložištěm artefaktů, aby se získaly podrobnosti o artefaktu virtuálního počítače, nastala chyba.  |
|  VMArtifactRepositoryInternalError  |  Při načítání dat artefaktů virtuálního počítače z úložiště artefaktů došlo k vnitřní chybě.  |
|  VMExtensionHandlerNonTransientError  |  Obslužná rutina "{0}"oznámil chybu pro rozšíření virtuálního počítače"{1}"s kódem chyby terminálu"{2}" a chybová zpráva: "{3}"  |
|  VMExtensionManagementInternalError  |  Došlo k vnitřní chybě při zpracování rozšíření virtuálního počítače "{0}".  |
|  VMExtensionManagementInternalError  |  Při přípravě rozšíření virtuálního počítače došlo k několika chybám. Zobrazit zobrazení instance virtuálního počítače rozšíření podrobnosti.  |
|  VMExtensionProvisioningError  |  Virtuální počítač nahlásil chybu při zpracování rozšíření "{0}". Chybová zpráva: "{1}".  |
|  VMExtensionProvisioningError  |  Několik rozšíření virtuálního počítače se nepovedlo zřídit ve virtuálním počítači. Podrobnosti najdete na zobrazení instance virtuálního počítače rozšíření podrobnosti.  |
|  VMExtensionProvisioningTimeout  |  Zřizování rozšíření virtuálního počítače "{0}' vypršení časového limitu. Instalace rozšíření možná trvá příliš dlouho, nebo nebylo možné získat stav rozšíření.  |
|  VMMarketplaceInvalidInput  |  Vytvoření virtuálního počítače z image Marketplace bez není nutné plánovat informace, odeberte prosím informace o plánu v požadavku. Název disku s operačním systémem je {0}.  |
|  VMMarketplaceInvalidInput  |  Informace o nákupu se neshoduje. Nelze nasadit z Marketplace image. Název disku s operačním systémem je {0}.  |
|  VMMarketplaceInvalidInput  |  Vytvoření virtuálního počítače z image z Marketplace vyžaduje informace plánu v požadavku. Název disku s operačním systémem je {0}.  |
|  VMNotFound  |  Virtuální počítač "{0}' nebyl nalezen.  |
|  VMRedeploymentFailed  |  Virtuální počítač "{0}' opětovné nasazení selhala kvůli vnitřní chybě. Zkuste to prosím znovu později.  |
|  VMRedeploymentTimedOut  |  Opětovné nasazení virtuálního počítače "{0}" neskončila v přiděleném čase. To může být úspěšně dokončit v nějakou dobu. Jinak můžete tento požadavek zopakovat.  |
|  VMStartTimedOut  |  Virtuální počítač "{0}' nebyla spuštěna v přiděleném čase. Virtuální počítač stále může úspěšně spustit. Zkontrolujte prosím stav napájení později.  |


## <a name="next-steps"></a>Další postup
Pokud potřebujete další pomoc, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.