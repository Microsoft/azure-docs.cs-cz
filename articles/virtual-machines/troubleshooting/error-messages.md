---
title: Běžné kódy chyb virtuálních počítačů v Azure | Microsoft Docs
description: Pochopení některých běžných kódů chyb, ke kterým došlo při zřizování a správě virtuálních počítačů v Azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: f5639d1cf94c77d699dc6de9841698b045ac1f96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76543014"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Vysvětlení běžných chybových zpráv při správě virtuálních počítačů v Azure

Tento článek popisuje některé z nejběžnějších chybových kódů a zpráv, se kterými se můžete setkat při vytváření nebo správě virtuálních počítačů v Azure.

>[!NOTE]
> Komentáře můžete na této stránce ponechat pro zpětnou vazbu nebo prostřednictvím [zpětné vazby Azure](https://feedback.azure.com/forums/216843-virtual-machines) pomocí značky #azerrormessage.

## <a name="error-response-format"></a>Formát odpovědi na chyby 
Virtuální počítače Azure používají pro chybovou odpověď následující formát JSON:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner level error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Odpověď na chybu vždy zahrnuje stavový kód a objekt chyby. Každý objekt chyby vždy obsahuje kód chyby a zprávu. Pokud se virtuální počítač vytvoří se šablonou, objekt Error obsahuje také část s podrobnostmi, která obsahuje vnitřní úroveň chybových kódů a zpráv. Obvykle je nejzákladnější vnitřní úroveň chybové zprávy kořenovou chybou.


## <a name="common-virtual-machine-management-errors"></a>Běžné chyby správy virtuálních počítačů

V této části jsou uvedené běžné chybové zprávy, se kterými se můžete setkat při správě virtuálních počítačů:

|  Kód chyby  |  Chybová zpráva  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nepovedlo se získat zapůjčení při{0}vytváření disku pomocí objektu BLOB {1}s identifikátorem URI. Objekt BLOB se už používá.  |  
|  AllocationFailed  |  Přidělení nebylo úspěšné. Zkuste prosím zmenšit velikost virtuálního počítače nebo počet virtuálních počítačů, zkuste to znovu později nebo to zkuste nasadit do jiné skupiny dostupnosti nebo jiného umístění Azure.  |  
|  AllocationFailed  |  Přidělení virtuálního počítače se nezdařilo z důvodu vnitřní chyby. Zkuste to prosím znovu později nebo to zkuste nasadit do jiného umístění.  |
|  ArtifactNotFound  |  Rozšíření virtuálního počítače s vydavatelem{0}a typem{1}nebylo v umístění{2}nalezeno.  |
|  ArtifactNotFound  |  V úložišti rozšíření nebylo{0}Nalezeno rozšíření s vydavatelem, typem{1}a verzí{2}obslužné rutiny typu.  |
|  ArtifactVersionNotFound  |  V úložišti artefaktů se nenašla žádná verze, která by splňovala požadovanou{0}verzi.  |
|  ArtifactVersionNotFound  |  V úložišti artefaktů se nenašla žádná verze, která by splňovala požadovanou{0}verzi pro rozšíření virtuálního počítače s vydavatelem{1}a typem.{2}  |
|  AttachDiskWhileBeingDetached  |  Datový disk{0}nelze připojit k virtuálnímu počítači{1}, protože disk je momentálně odpojen. Počkejte prosím, než se disk úplně odpojí, a zkuste to znovu.  |
|  Důvodu chybného požadavku  |  Zarovnané skupiny dostupnosti se zatím v této oblasti nepodporují.  |
|  Důvodu chybného požadavku  |  Přidání virtuálního počítače se spravovanými disky do nespravované skupiny dostupnosti nebo přidání virtuálního počítače s disky založenými na objektech blob do spravované skupiny dostupnosti se nepodporuje. Vytvořte prosím skupinu dostupnosti se sadou vlastností Managed, aby se do ní přidal virtuální počítač se spravovanými disky.  |
|  Důvodu chybného požadavku  |  Managed Disks se v této oblasti nepodporují.  |
|  Důvodu chybného požadavku  |  Více VMExtensions na obslužnou rutinu není podporováno pro typ{0}operačního systému. {1}VMExtension s obslužným rutinou{2}se už přidal nebo zadal ve vstupu.  |
|  Důvodu chybného požadavku  |  {0}Operace se u prostředku{1}se spravovanými disky nepodporuje.  |
|  CertificateImproperlyFormatted  |  Reprezentace JSON tajného klíče načtená {0} z má datové pole, které není správně FORMÁTOVANÝ soubor PFX, nebo zadané heslo nekóduje soubor PFX správně.  |
|  CertificateImproperlyFormatted  |  Data načtená z {0} nelze deserializovat do formátu JSON.  |
|  Konflikt  |  Změna velikosti disku je povolená jenom při vytváření virtuálního počítače nebo při navráceném virtuálním počítači.  |
|  ConflictingUserInput  |  {0}Disk nelze připojit, protože disk je již vlastněn virtuálním{1}počítačem.  |
|  ConflictingUserInput  |  Zdrojové a cílové skupiny prostředků jsou stejné.  |
|  ConflictingUserInput  |  Zdrojové a cílové účty úložiště pro disk {0} se liší.  |
|  ContainerAlreadyOnLease  |  V kontejneru úložiště, který obsahuje objekt BLOB s identifikátorem URI {0}, již existuje zapůjčení.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Požadavek na přesunutí prostředků obsahuje prostředky trezoru klíčů, na které odkazuje jeden nebo {0}více s v žádosti. Tato možnost není v současné době v přesunu mezi předplatnými podporována. Podívejte se prosím na podrobnosti o chybách pro ID prostředků trezoru klíčů.  |
|  DiagnosticsOperationInternalError  |  Při zpracování diagnostického profilu virtuálního počítače {0}došlo k vnitřní chybě.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Objekt {0} BLOB je již používán jiným diskem, který patří do{1}virtuálního počítače. Můžete kontrolovat metadata objektu BLOB pro informace o odkazech na disk.  |
|  DiskBlobNotFound  |  Nepodařilo se najít objekt BLOB VHD s {0} identifikátorem URI{1}pro disk.  |
|  DiskBlobNotFound  |  Nepovedlo se najít objekt BLOB {0}VHD s identifikátorem URI.  |
|  DiskEncryptionKeySecretMissingTags  |  {0}tajný kód neobsahuje {1} značky. Aktualizujte prosím tajnou verzi, přidejte požadované značky a zkuste to znovu.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Nepovedlo se {0} rozbalení tajné {1} hodnoty pomocí klíče.  |
|  DiskImageNotReady  |  Bitová {0} kopie disku {1} je ve stavu. Zkuste to prosím znovu, až bude obrázek připravený.  |
|  DiskPreparationError  |  Při přípravě disků virtuálního počítače došlo k jedné nebo více chybám. Podrobnosti najdete v tématu zobrazení instance disku.  |
|  DiskProcessingError  |  Zpracování disku bylo zastaveno, protože virtuální počítač má jiné disky v neúspěšných discích.  |
|  ImageBlobNotFound  |  Nepodařilo se najít objekt BLOB VHD s {0} identifikátorem URI{1}pro disk.  |
|  ImageBlobNotFound  |  Nepovedlo se najít objekt BLOB {0}VHD s identifikátorem URI.  |
|  IncorrectDiskBlobType  |  Objekty blob disku můžou být jenom typu objekt blob stránky. Objekt {0} BLOB pro disk{1}je typu objekt blob bloku.  |
|  IncorrectDiskBlobType  |  Objekty blob disku můžou být jenom typu objekt blob stránky. Objekt {0} BLOB je typu{1}.  |
|  IncorrectImageBlobType  |  Objekty blob disku můžou být jenom typu objekt blob stránky. Objekt {0} BLOB pro disk{1}je typu objekt blob bloku.  |
|  IncorrectImageBlobType  |  Objekty blob disku můžou být jenom typu objekt blob stránky. Objekt {0} BLOB je typu{1}.  |
|  InternalOperationError  |  Nepovedlo se přeložit {0}účet úložiště. Ujistěte se prosím, že se vytvořila prostřednictvím poskytovatele prostředků úložiště ve stejném umístění jako výpočetní prostředek.  |
|  InternalOperationError  |  {0}úkoly, které hledají cíl, se nezdařily.  |
|  InternalOperationError  |  Při ověřování profilu sítě virtuálního počítače{0}došlo k chybě.  |
|  InvalidAccountType  |  AccountType {0} je neplatný.  |
|  InvalidParameter  |  Hodnota parametru {0} je neplatná.  |
|  InvalidParameter  |  Zadané heslo správce není povoleno.  |
|  InvalidParameter  |  "Zadané heslo musí mít délku mezi {0}{1} znaky a musí splňovat alespoň {2} požadavky na složitost hesla z následujících možností: <ol><li> Obsahuje znak velkými písmeny</li><li>Obsahuje malý znak</li><li>Obsahuje číselnou číslici</li><li>Obsahuje speciální znak.</li></ol>  |
|  InvalidParameter  |  Zadané uživatelské jméno správce není povoleno.  |
|  InvalidParameter  |  Pokud je virtuální počítač vytvořený z platformy nebo uživatelské image, nejde připojit stávající disk s operačním systémem.  |
|  InvalidParameter  |  Název {0} kontejneru je neplatný. Názvy kontejnerů musí mít délku 3-63 znaků a můžou obsahovat jenom malé alfanumerické znaky a spojovníky. Před spojovníkem musí následovat alfanumerický znak.  |
|  InvalidParameter  |  Název {0} kontejneru v adrese {1} URL je neplatný. Názvy kontejnerů musí mít délku 3-63 znaků a můžou obsahovat jenom malé alfanumerické znaky a spojovníky. Před spojovníkem musí následovat alfanumerický znak.  |
|  InvalidParameter  |  Název objektu BLOB v adrese {0} URL obsahuje lomítko. To je pro disky aktuálně nepodporované.  |
|  InvalidParameter  |  Identifikátor URI {0} nevypadá za správný identifikátor URI objektu BLOB.  |
|  InvalidParameter  |  Disk s názvem{0}už používá stejnou logickou jednotku (LUN {1}):.  |
|  InvalidParameter  |  Disk s názvem{0}už existuje.  |
|  InvalidParameter  |  Nejde zadat přepsání uživatelských imagí pro disk, který už je definovaný v zadaném odkazu na obrázek.  |
|  InvalidParameter  |  Disk s názvem{0}už používá stejnou adresu URL {1}virtuálního pevného disku.  |
|  InvalidParameter  |  Zadaný počet {0} domén selhání musí klesnout do rozsahu {1} {2}.  |
|  InvalidParameter  |  Typ {0} licence je neplatný. Platné typy licencí jsou: Windows_Client nebo Windows_Server, rozlišuje velká a malá písmena.  |
|  InvalidParameter  |  Název hostitele Linux nesmí být {0} delší než znaků nebo obsahovat následující znaky: {1}.  |
|  InvalidParameter  |  Cílová cesta pro veřejné klíče SSH je aktuálně omezená na výchozí hodnotu {0} z důvodu známého problému v agentu zřizování Linux.  |
|  InvalidParameter  |  Disk na logické jednotce {0} (LUN) již existuje.  |
|  InvalidParameter  |  Odběr {0} žádosti se musí shodovat s předplatným {1} obsaženým v ID spravovaného disku.  |
|  InvalidParameter  |  Vlastní data v OSProfile musí být v kódování Base64 a maximální délka {0} znaků.  |
|  InvalidParameter  |  Název objektu BLOB v {0} adrese URL musí končit{1}příponou.  |
|  InvalidParameter  |  {0}' není platná Předpona názvu objektu BLOB s zachyceným virtuálním pevným diskem. Platná předpona odpovídá regulárnímu{1}výrazu.  |
|  InvalidParameter  |  Pokud není zřízen agent virtuálního počítače, nelze do svého virtuálního počítače přidat certifikáty.  |
|  InvalidParameter  |  Disk na logické jednotce {0} (LUN) již existuje.  |
|  InvalidParameter  |  Virtuální počítač se nepovedlo vytvořit, protože {0} požadovaná velikost není v clusteru, kde je aktuálně přidělená Skupina dostupnosti, dostupná. Dostupné velikosti jsou: {1}. Další informace o strategii změny velikosti virtuálních počítačů https://aka.ms/azure-resizevmnajdete na adrese.  |
|  InvalidParameter  |  Požadovaná velikost {0} virtuálního počítače není v aktuální oblasti k dispozici. Dostupné velikosti v aktuální oblasti jsou: {1}. Další informace o dostupných velikostech virtuálních počítačů v jednotlivých oblastech najdete https://aka.ms/azure-regionsv části.  |
|  InvalidParameter  |  Požadovaná velikost {0} virtuálního počítače není v aktuální oblasti k dispozici. Další informace o dostupných velikostech virtuálních počítačů v jednotlivých oblastech najdete https://aka.ms/azure-regionsv části.  |
|  InvalidParameter  |  Uživatelské jméno správce systému Windows nemůže být delší {0} než znaků, musí končit tečkou (.) nebo obsahovat následující znaky: {1}.  |
|  InvalidParameter  |  Název počítače s Windows nemůže být delší {0} než znaků, musí být zcela číslice nebo obsahovat následující znaky: {1}.  |
|  MissingMoveDependentResources  |  Požadavek na přesunutí prostředků neobsahuje všechny závislé prostředky. Zkontrolujte podrobnosti o chybě chybějících ID prostředků.  |
|  MoveResourcesHaveInvalidState  |  Požadavek na přesunutí prostředků obsahuje virtuální počítače, které jsou přidružené k neplatným účtům úložiště. Podrobnosti o těchto ID prostředků a odkazovaných názvech účtů úložiště najdete v podrobnostech.  |
|  MoveResourcesHavePendingOperations  |  Požadavek na přesunutí prostředků obsahuje prostředky, u kterých operace čeká na vyřízení. Zkontrolujte prosím podrobnosti těchto ID prostředků. Po dokončení probíhajících operací zkuste operaci zopakovat.  |
|  MoveResourcesNotFound  |  Požadavek na přesunutí prostředků obsahuje prostředky, které se nenašly. Zkontrolujte prosím podrobnosti těchto ID prostředků.  |
|  NetworkingInternalOperationError  |  Neznámá chyba přidělení sítě  |
|  NetworkingInternalOperationError  |  Neznámá chyba přidělení sítě  |
|  NetworkingInternalOperationError  |  Při zpracování síťového profilu virtuálního počítače došlo k vnitřní chybě.  |
|  NotFound  |  Skupinu {0} dostupnosti se nepovedlo najít.  |
|  NotFound  |  Zdrojový virtuální počítač{0}zadaný v žádosti v tomto umístění Azure neexistuje.  |
|  NotFound  |  Tenant s ID {0} nebyl nalezen.  |
|  NotFound  |  Obrázek {0} nejde najít.  |
|  NotSupported  |  Typ licence je {0}, ale objekt BLOB {1} image není místní.  |
|  OperationNotAllowed  |  Skupinu {0} dostupnosti nelze odstranit. Před odstraněním skupiny dostupnosti zajistěte, aby neobsahoval žádný virtuální počítač.  |
|  OperationNotAllowed  |  Změna SKU skupiny dostupnosti z aligned na Classic se nepovoluje.  |
|  OperationNotAllowed  |  Pokud není virtuální počítač spuštěný, nejde ve virtuálním počítači upravovat rozšíření.  |
|  OperationNotAllowed  |  Akce zachycení je podporována pouze ve virtuálním počítači s disky založenými na objektech blob. K vytvoření image ze spravovaného virtuálního počítače použijte prosím rozhraní API prostředků image.  |
|  OperationNotAllowed  |  Prostředek {0} nelze vytvořit z obrázku {1} , dokud se image úspěšně nevytvořila.  |
|  OperationNotAllowed  |  Aktualizace encryptionSettings nejsou povolené, když je virtuální počítač přidělený, zkuste to prosím znovu, až se virtuální počítač oddělí.  |
|  OperationNotAllowed  |  Přidání spravovaného disku do virtuálního počítače s disky založenými na objektech blob se nepodporuje.  |
|  OperationNotAllowed  |  Maximální počet datových disků, které je povoleno připojit k virtuálnímu počítači této velikosti, je {0}.  |
|  OperationNotAllowed  |  Přidání disku založeného na objektu blob do virtuálního počítače se spravovanými disky se nepodporuje.  |
|  OperationNotAllowed  |  {0}Operace není u bitové kopie{1}povolena, protože Image je označena pro odstranění. Operaci odstranění můžete opakovat jenom (nebo počkejte na dokončení probíhající operace).  |
|  OperationNotAllowed  |  {0}Operace se na virtuálním počítači{1}nepovoluje, protože virtuální počítač je zobecněný.  |
|  OperationNotAllowed  |  Operace '{0}' není povolena, protože kolekce bodů obnovení '{1}' je označena k odstranění.  |
|  OperationNotAllowed  |  {0}Operace není pro rozšíření{1}virtuálního počítače povolená, protože je označená k odstranění. Operaci odstranění můžete opakovat jenom (nebo počkejte na dokončení probíhající operace).  |
|  OperationNotAllowed  |  Operace '{0}' není povolena, protože Virtual Machines '{1}' jsou zřízeny pomocí image '{2}'.  |
|  OperationNotAllowed  |  {0}Operace není povolená, protože škálovací sady{1}virtuálního počítače aktuálně používá Image{2}.  |
|  OperationNotAllowed  |  {0}Operace se na virtuálním počítači{1}nepovoluje, protože virtuální počítač je označený k odstranění. Operaci odstranění můžete opakovat jenom (nebo počkejte na dokončení probíhající operace).  |
|  OperationNotAllowed  |  {0}Operace se na virtuálním počítači{1}nepovoluje, protože virtuální počítač se buď nezměnil, nebo je označený jako oddělované.  |
|  OperationNotAllowed  |  {0}Operace se na virtuálním počítači{1}nepovoluje, protože virtuální počítač je spuštěný. Pro případ, že virtuální počítač vypnete uvnitř hostovaného operačního systému, vypněte prosím explicitní napájení.  |
|  OperationNotAllowed  |  {0}Operace se na virtuálním počítači{1}nepovoluje, protože virtuální počítač není navrácený.  |
|  OperationNotAllowed  |  {0}Operace není u virtuálního počítače{1}povolená, protože virtuální počítač má rozšíření{2}v neúspěšném stavu.  |
|  OperationNotAllowed  |  {0}Operace není u virtuálního počítače{1}povolena, protože probíhá jiná operace.  |
|  OperationNotAllowed  |  Operace{0}vyžaduje zobecnění virtuálního počítače{1}.  |
|  OperationNotAllowed  |  Operace vyžaduje, aby byl virtuální počítač spuštěný (nebo nastavený jako spuštěný).  |
|  OperationNotAllowed  |  Disk s velikostí {0}GB, který je menší než velikost {1}GB odpovídajícího disku v imagi, není povolen.  |
|  OperationNotAllowed  |  Rozšíření obslužné rutiny{0}sady škálování virtuálního počítače se dají přidat jenom v době vytváření sady škálování virtuálního počítače.  |
|  OperationNotAllowed  |  Rozšíření obslužné rutiny{0}sady škálování virtuálního počítače se dá odstranit jenom v době odstranění sady škálování virtuálního počítače.  |
|  OperationNotAllowed  |  {0}Virtuální počítač už používá spravované disky.  |
|  OperationNotAllowed  |  {0}Virtuální počítač patří do skupiny{1}dostupnosti Classic. Aktualizujte prosím skupinu dostupnosti tak, aby používala SKU zarovnaná a pak zkuste převod zopakovat.  |
|  OperationNotAllowed  |  Virtuální počítač vytvořený z image nemůže mít disky založené na objektech blob. Všechny disky musí být spravované disky.  |
|  OperationNotAllowed  |  Operaci zachycení nejde dokončit, protože virtuální počítač není zobecněný.  |
|  OperationNotAllowed  |  Operace správy na virtuálním{0}počítači jsou zakázané, protože disky virtuálního počítače se převádějí na spravované disky.  |
|  OperationNotAllowed  |  Probíhající operace mění stav napájení virtuálního počítače {0} na. {1} Po nějaké době {2} prosím zkuste operaci provést.  |
|  OperationNotAllowed  |  Nepovedlo se přidat nebo aktualizovat virtuální počítač. Požadovaná velikost {0} virtuálního počítače nemusí být k dispozici v existující alokační jednotce. Další informace o strategii změny velikosti virtuálních počítačů https://aka.ms/azure-resizevmnajdete na adrese.  |
|  OperationNotAllowed  |  Nepovedlo se změnit velikost virtuálního počítače, {0} protože požadovaná velikost není v clusteru, kde je aktuálně přidělená Skupina dostupnosti, dostupná. Dostupné velikosti jsou: {1}. Další informace o strategii změny velikosti virtuálních počítačů https://aka.ms/azure-resizevmnajdete na adrese.  |
|  OperationNotAllowed  |  Nepovedlo se změnit velikost virtuálního počítače, {0} protože požadovaná velikost není dostupná v clusteru, ve kterém je virtuální počítač aktuálně přidělený. Pokud chcete změnit velikost virtuálního {1} počítače, abyste ho nastali, zrušte jeho přidělení (Jedná se o operaci zastavení v Azure Portal) a zkuste operaci změny velikosti zopakovat. Další informace o strategii změny velikosti virtuálních počítačů https://aka.ms/azure-resizevmnajdete na adrese.  |
|  OSProvisioningClientError  |  Zřizování operačního systému pro virtuální počítač{0}se nepovedlo, protože hostovaný operační systém se v tuto chvíli zřizuje.  |
|  OSProvisioningClientError  |  Zřizování operačního systému pro virtuální počítač{0}se nepovedlo. Podrobnosti o chybě {1} : Ujistěte se, že je bitová kopie správně připravená (zobecněná). <ul><li>Pokyny pro Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Nepovedlo se vygenerovat klíč hostitele SSH. Podrobnosti o chybě {0}:. Chcete-li vyřešit tento problém, ověřte, zda je nainstalován agent pro Linux. <ul><li>Pokyny najdete v těchto pokynech:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Uživatelské jméno zadané pro virtuální počítač je pro tuto distribuci systému Linux neplatné. Podrobnosti o chybě {0}:.  |
|  OSProvisioningInternalError  |  Zřizování operačního systému se pro virtuální počítač{0}nepovedlo kvůli vnitřní chybě.  |
|  OSProvisioningTimedOut  |  Zřizování operačního systému pro virtuální počítač{0}se nedokončilo ve vymezeném čase. Virtuální počítač se přesto může úspěšně dokončit. Zkontrolujte prosím stav zřizování později.  |
|  OSProvisioningTimedOut  |  Zřizování operačního systému pro virtuální počítač{0}se nedokončilo ve vymezeném čase. Virtuální počítač se přesto může úspěšně dokončit. Zkontrolujte prosím stav zřizování později. Také se ujistěte, že je bitová kopie správně připravená (zobecněná).   <ul><li>Pokyny pro Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Pokyny pro Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Zřizování operačního systému pro virtuální počítač{0}se nedokončilo ve vymezeném čase. Agent hosta virtuálního počítače se ale zjistil jako spuštěný. To naznačuje, že hostovaný operační systém nebyl správně připravený pro použití jako image virtuálního počítače (s CreateOption = FromImage). Tento problém vyřešíte tak, že virtuální pevný disk použijete s CreateOption = připojit nebo ho Připravte správně, aby bylo možné ho použít jako obrázek:   <ul><li>Pokyny pro Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Pokyny pro Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Požadovaná velikost virtuálního počítače není aktuálně k dispozici ve vybraném umístění.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Prostředek nejde v tuto chvíli aktualizovat z důvodu probíhající aktualizace platformy. Zkuste to prosím znovu později.  |
|  StorageAccountLimitation  |  Účet{0}úložiště nepodporuje objekty blob stránky, které jsou potřeba k vytváření disků.  |
|  StorageAccountLimitation  |  Účet{0}úložiště překročil přidělenou kvótu.  |
|  StorageAccountLocationMismatch  |  Nepovedlo se přeložit {0}účet úložiště. Ujistěte se prosím, že se vytvořila prostřednictvím poskytovatele prostředků úložiště ve stejném umístění jako výpočetní prostředek.  |
|  StorageAccountNotFound  |  Účet {0} úložiště se nenašel. Ujistěte se, že se účet úložiště neodstranil a že patří do stejného umístění Azure jako virtuální počítač.  |
|  StorageAccountNotRecognized  |  Použijte prosím účet úložiště, který spravuje poskytovatel prostředků úložiště. Použití {0} není podporováno.  |
|  StorageAccountOperationInternalError  |  Při přístupu k účtu {0}úložiště došlo k vnitřní chybě.  |
|  StorageAccountSubscriptionMismatch  |  Účet {0} úložiště nepatří k předplatnému {1}.  |
|  StorageAccountTooBusy  |  Účet úložiště{0}' ' je aktuálně zaneprázdněn. Zvažte použití jiného účtu.  |
|  StorageAccountTypeNotSupported  |  Využití {0} {1} disku, což je účet Blob Storage. Zkuste to prosím znovu s účtem úložiště pro obecné účely.  |
|  StorageAccountTypeNotSupported  |  Účet {0} úložiště je {1} typu. Diagnostika spouštění podporuje {2} typy účtů úložiště.  <ul><li>K této chybě dochází, pokud pro diagnostiku spouštění použijete účet Premium Storage. Další informace najdete v tématu [Jak používat diagnostiku spouštění](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Předplatné není autorizované.  |
|  TargetDiskBlobAlreadyExists  |  Objekt {0} BLOB již existuje. Zadejte prosím jiný identifikátor URI objektu BLOB pro vytvoření nového prázdného datového disku{1}.  |
|  TargetDiskBlobAlreadyExists  |  Operace zachycení nemůže pokračovat, protože objekt BLOB {0} cílové image už existuje a příznak pro přepsání objektů BLOB VHD není nastavený. Buď odstraňte objekt blob, nebo nastavte příznak pro přepsání objektů BLOB VHD a zkuste to znovu.  |
|  TargetDiskBlobAlreadyExists  |  Operace zachycení nemůže pokračovat, protože na cílovém {0} objektu BLOB Image je aktivní zapůjčení.   |
|  TargetDiskBlobAlreadyExists  |  Objekt {0} BLOB již existuje. Zadejte prosím jako cíl pro disk{1}jiný identifikátor URI objektu BLOB.  |
|  TooManyVMRedeploymentRequests  |  Pro virtuální počítač{0}nebo virtuální počítače ve stejné availabilityset s tímto virtuálním počítačem byly přijaty příliš mnoho požadavků na opětovné nasazení. Zkuste to prosím znovu později.  |
|  VHDSizeInvalid  |  Zadaná hodnota velikosti disku {0} pro disk{1}s objektem BLOB {2} není platná. Velikost disku musí být mezi {3} a {4}.  |
|  VMAgentStatusCommunicationError  |  {0}Virtuální počítač nenahlásil stav pro agenta nebo rozšíření virtuálního počítače. Ověřte prosím, jestli má virtuální počítač spuštěného agenta virtuálních počítačů, a může navázat odchozí připojení k úložišti Azure.  |
|  VMArtifactRepositoryInternalError  |  Při komunikaci s úložištěm artefaktů došlo k chybě, aby bylo možné získat podrobnosti o artefaktech virtuálních počítačů.  |
|  VMArtifactRepositoryInternalError  |  Při načítání dat artefaktů virtuálních počítačů z úložiště artefaktů došlo k vnitřní chybě.  |
|  VMExtensionHandlerNonTransientError  |  {0}Obslužná rutina ohlásila selhání pro rozšíření{1}virtuálního počítače s kódem{2}chyby terminálu a chybovou zprávou:{3}  |
|  VMExtensionManagementInternalError  |  Došlo k vnitřní chybě při zpracovávání rozšíření{0}virtuálního počítače.  |
|  VMExtensionManagementInternalError  |  Při přípravě rozšíření virtuálních počítačů došlo k více chybám. Podrobnosti najdete v tématu zobrazení instance rozšíření virtuálních počítačů.  |
|  VMExtensionProvisioningError  |  Virtuální počítač oznámil chybu při zpracování rozšíření{0}' '. Chybová zpráva: "{1}".  |
|  VMExtensionProvisioningError  |  Na virtuálním počítači se nepovedlo zřídit víc rozšíření virtuálního počítače. Podrobnosti najdete v zobrazení instance rozšíření virtuálních počítačů.  |
|  VMExtensionProvisioningTimeout  |  Při zřizování rozšíření{0}virtuálního počítače vypršel časový limit. Instalace rozšíření může trvat příliš dlouho nebo nejde získat stav rozšíření.  |
|  VMMarketplaceInvalidInput  |  Vytvoření virtuálního počítače z image mimo Marketplace nepotřebuje informace o plánu, odeberte prosím informace plánu v žádosti. Název disku operačního systému {0}je.  |
|  VMMarketplaceInvalidInput  |  Informace o nákupu se neshodují. Z image Marketplace nejde nasadit. Název disku operačního systému {0}je.  |
|  VMMarketplaceInvalidInput  |  Vytvoření virtuálního počítače z image Marketplace vyžaduje informace o plánu v žádosti. Název disku operačního systému {0}je.  |
|  VMNotFound  |  Virtuální počítač{0}se nepovedlo najít.  |
|  VMRedeploymentFailed  |  {0}Opětovné nasazení virtuálního počítače se nezdařilo z důvodu vnitřní chyby. Zkuste to prosím znovu později.  |
|  VMRedeploymentTimedOut  |  Opětovné nasazení virtuálního počítače{0}se nedokončilo ve vyhrazeném čase. Může dojít k úspěšnému dokončení v některých případě. Jinak můžete žádost opakovat.  |
|  VMStartTimedOut  |  {0}Virtuální počítač se nespustil ve vyhrazeném čase. Virtuální počítač se může pořád úspěšně spustit. Zkontrolujte prosím stav napájení později.  |


## <a name="next-steps"></a>Další kroky
Pokud potřebujete další informace, můžete se obrátit na odborníky na Azure na [webu MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
