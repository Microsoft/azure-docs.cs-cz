---
title: Běžné kódy chyb virtuálních montovna v Azure | Dokumenty společnosti Microsoft
description: Seznamte se s některými běžnými kódy chyb, které se vyskytly při zřizování a správě virtuálních počítačů v Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543014"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Vysvětlení běžných chybových zpráv při správě virtuálních počítačů v Azure

Tento článek popisuje některé nejběžnější chybové kódy a zprávy, se kterými se můžete setkat při vytváření nebo správě virtuálních počítačů (VM) v Azure.

>[!NOTE]
> Komentáře na této stránce můžete zanechat pro zpětnou vazbu nebo prostřednictvím [zpětné vazby Azure](https://feedback.azure.com/forums/216843-virtual-machines) se značkou #azerrormessage.

## <a name="error-response-format"></a>Formát odpovědi na chybu 
Virtuální počítače Azure používají následující formát JSON pro odpověď na chybu:

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

Odpověď na chybu vždy obsahuje stavový kód a objekt chyby. Každý objekt chyby vždy obsahuje kód chyby a zprávu. Pokud je virtuální jazyk vytvořen pomocí šablony, objekt chyby také obsahuje oddíl podrobností, který obsahuje vnitřní úroveň chybových kódů a zprávy. Za normálních okolností je nejvíce vnitřní úroveň chybové zprávy selhání kořenového adresáře.


## <a name="common-virtual-machine-management-errors"></a>Běžné chyby správy virtuálních strojů

V této části jsou uvedeny běžné chybové zprávy, se kterými se můžete setkat při správě virtuálních měn:

|  Kód chyby  |  Chybová zpráva  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Při vytváření disku pomocí{0}objektu BLOB {1}s identifikátorem URI se nepodařilo získat zapůjčení . Objekt blob je již používán.  |  
|  Přidělení se nezdařilo.  |  Přidělení se nezdařilo. Zkuste zmenšit velikost virtuálního počítače nebo počet virtuálních počítačích, opakujte akci později nebo zkuste nasazení do jiné sady dostupnosti nebo do jiného umístění Azure.  |  
|  Přidělení se nezdařilo.  |  Přidělení virtuálního objektu se nezdařilo z důvodu vnitřní chyby. Zkuste to zopakovat později nebo zkuste nasazení do jiného umístění.  |
|  Artefaktnotfound  |  Rozšíření virtuálního virtuálního{0}řezu{1}s vydavatelem ' a{2}typem ' ' nebylo nalezeno v umístění .  |
|  Artefaktnotfound  |  Rozšíření s{0}vydavatelem '{1}', zadejte{2}' ' a verze obslužné rutiny typu ' ' nebyl nalezen v úložišti rozšíření.  |
|  ArtefaktVersionNotFound  |  V úložišti artefaktů nebyla nalezena žádná{0}verze, která by splňovala požadovanou verzi .  |
|  ArtefaktVersionNotFound  |  V úložišti artefaktů nebyla nalezena žádná{0}verze, která splňuje{1}požadovanou verzi{2}" ' pro rozšíření virtuálního her s vydavatelem ' a zadejte ' .  |
|  AttachDiskWhileBeingDetached  |  Datový disk '{0}' k{1}virtuálnímu počítače ' nelze připojit, protože disk je právě odpojen. Počkejte, až bude disk zcela odpojen, a akci opakujte.  |
|  Chybný požadavek  |  Zarovnané ' Dostupnost sady ještě nejsou podporovány v této oblasti.  |
|  Chybný požadavek  |  Přidání virtuálního počítače se spravovanými disky do nespravované sady dostupnosti nebo přidání virtuálního počítače s disky založenými na objektech blob do spravované sady dostupnosti není podporováno. Vytvořte sadu dostupnosti se sadou vlastností "spravované", abyste do něj mohli přidat virtuální modul se spravovanými disky.  |
|  Chybný požadavek  |  Spravované disky nejsou v této oblasti podporovány.  |
|  Chybný požadavek  |  Více rozšíření virtuálního řezu na obslužnou rutinu není podporováno pro typ operačního systému .{0} Rozšíření vmextension '{1}{2}' s obslužnou rutinou ' ' již bylo přidáno nebo zadáno ve vstupu.  |
|  Chybný požadavek  |  Operace{0}' ' není podporována u prostředků '{1}' se spravovanými disky.  |
|  Certifikát nesprávně formátovaný  |  Reprezentace JSON načtená z {0} tajného klíče má datové pole, které není správně formátovaný soubor PFX, nebo zadaný hesel nedekóduje soubor PFX správně.  |
|  Certifikát nesprávně formátovaný  |  Data načtená {0} z nelze rekonstruovat do JSON.  |
|  Konflikt  |  Změna velikosti disku je povolená jenom při vytváření virtuálního počítače nebo při vzdáleném umístění virtuálního počítače.  |
|  Konfliktnívstup uživatele  |  Disk{0}' ' nelze připojit, protože disk je{1}již vlastněn virtuálním počítačem .  |
|  Konfliktnívstup uživatele  |  Skupiny zdrojových a cílových prostředků jsou stejné.  |
|  Konfliktnívstup uživatele  |  Účty zdrojového a {0} cílového úložiště pro disk se liší.  |
|  ContainerAlreadyOnLease  |  V kontejneru úložiště, který obsahuje objekt blob s identifikátorem URI {0}, již existuje zapůjčení .  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Požadavek přesunout prostředky obsahuje prostředky KeyVault, {0}na které odkazuje jeden nebo více s v požadavku. To není podporováno aktuálně v křížové předplatné Přesunout. Zkontrolujte podrobnosti o chybě id prostředků KeyVault.  |
|  DiagnosticsOperationInternalError  |  Při zpracování diagnostického profilu virtuálního {0}aplikace došlo k vnitřní chybě.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Objekt {0} Blob je již používán jiným diskem patřícím virtuálnímu počítače .{1} Můžete zkontrolovat metadata objektu blob pro informace reference disku.  |
|  DiskBlobNotnebyl nalezen  |  Nelze najít objekt blob {0} virtuálního{1}pevného disku s identifikátorem URI pro disk .  |
|  DiskBlobNotnebyl nalezen  |  Nelze najít objekt blob {0}virtuálního pevného disku s identifikátorem URI .  |
|  DiskEncryptionKeySecretChybějícítagy  |  {0}Tajemství nemá značky. {1} Aktualizujte tajnou verzi, přidejte požadované značky a opakujte akci.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Rozbalení tajné {0} hodnoty {1} pomocí klíče se nezdařilo.  |
|  DiskImageNotReady  |  Obrázek {0} disku {1} je ve stavu. Opakujte akci, jakmile je obraz připraven.  |
|  Chyba přípravy disku  |  Při přípravě disků virtuálních počítačů došlo k jedné nebo více chybám. Podrobnosti naleznete v zobrazení instance disku.  |
|  Chyba zpracování disku  |  Zpracování disku bylo zastaveno, protože virtuální modul má další disky na neúspěšných discích.  |
|  ImageBlobNotFound  |  Nelze najít objekt blob {0} virtuálního{1}pevného disku s identifikátorem URI pro disk .  |
|  ImageBlobNotFound  |  Nelze najít objekt blob {0}virtuálního pevného disku s identifikátorem URI .  |
|  Nesprávná doba blobtype  |  Objekty BLOB disku mohou být pouze typu objekt blob stránky. Objekt {0} blob{1}pro disk ' ' je typu bloku objektblob.  |
|  Nesprávná doba blobtype  |  Objekty BLOB disku mohou být pouze typu objekt blob stránky. Objekt {0} blob je{1}typu ' '.  |
|  NesprávněImageBlobType  |  Objekty BLOB disku mohou být pouze typu objekt blob stránky. Objekt {0} blob{1}pro disk ' ' je typu bloku objektblob.  |
|  NesprávněImageBlobType  |  Objekty BLOB disku mohou být pouze typu objekt blob stránky. Objekt {0} blob je{1}typu ' '.  |
|  Chyba InternalOperationError  |  Účet úložiště nelze {0}vyřešit. Ujistěte se, že byla vytvořena prostřednictvím zprostředkovatele prostředků úložiště ve stejném umístění jako výpočetní prostředek.  |
|  Chyba InternalOperationError  |  {0}úkoly hledání cílů se nezdařily.  |
|  Chyba InternalOperationError  |  Při ověřování síťového profilu virtuálního{0}připojení došlo k chybě.  |
|  Neplatný typ účtu  |  AccountType {0} je neplatný.  |
|  Parametr InvalidParameter  |  Hodnota parametru {0} je neplatná.  |
|  Parametr InvalidParameter  |  Zadané heslo správce není povoleno.  |
|  Parametr InvalidParameter  |  "Zadané heslo musí {0}být{1} mezi - znaky {2} dlouhé a musí splňovat alespoň požadavky na složitost hesla z následujících: <ol><li> Obsahuje znak velká písmena.</li><li>Obsahuje znak s malou písmena.</li><li>Obsahuje číselnou číslici.</li><li>Obsahuje speciální znak.</li></ol>  |
|  Parametr InvalidParameter  |  Zadané uživatelské jméno správce není povoleno.  |
|  Parametr InvalidParameter  |  Nelze připojit existující disk operačního systému, pokud je virtuální modul vytvořen z platformy nebo image uživatele.  |
|  Parametr InvalidParameter  |  Název {0} kontejneru je neplatný. Názvy kontejnerů musí mít délku 3 až 63 znaků a mohou obsahovat pouze malá alfanumerická znavek a pomlčka. Před alfanumerickým znakem musí být předchází a musí za ním následovat alfanumerický znak.  |
|  Parametr InvalidParameter  |  Název {0} kontejneru {1} v adrese URL je neplatný. Názvy kontejnerů musí mít délku 3 až 63 znaků a mohou obsahovat pouze malá alfanumerická znavek a pomlčka. Před alfanumerickým znakem musí být předchází a musí za ním následovat alfanumerický znak.  |
|  Parametr InvalidParameter  |  Název objektu blob v adrese URL {0} obsahuje lomítko. To to není v současné době podporováno pro disky.  |
|  Parametr InvalidParameter  |  Identifikátor {0} URI nevypadá jako správný identifikátor URI objektu BLOB.  |
|  Parametr InvalidParameter  |  Disk s{0}názvem ' ' již {1}používá stejnou logickou jednotku: .  |
|  Parametr InvalidParameter  |  Disk s{0}názvem ' ' již existuje.  |
|  Parametr InvalidParameter  |  Nelze určit přepsání obrazu uživatele pro disk, který je již definován v zadaném odkazu na obrázek.  |
|  Parametr InvalidParameter  |  Disk s{0}názvem ' ' již {1}používá stejnou adresu URL virtuálního pevného disku .  |
|  Parametr InvalidParameter  |  Zadaný počet {0} domén selhání musí {1} {2}spadat do rozsahu do .  |
|  Parametr InvalidParameter  |  Typ {0} licence je neplatný. Platné typy licencí jsou: Windows_Client nebo Windows_Server, rozlišují malá a velká písmena.  |
|  Parametr InvalidParameter  |  Název hostitele Linuxu nesmí překročit {0} délku {1}znaků ani obsahovat následující znaky: .  |
|  Parametr InvalidParameter  |  Cílová cesta pro veřejné klíče Ssh je {0} aktuálně omezena na jeho výchozí hodnotu z důvodu známého problému v agenta zřizování Linuxu.  |
|  Parametr InvalidParameter  |  Disk na {0} logické jednotce již existuje.  |
|  Parametr InvalidParameter  |  Odběr {0} požadavku musí odpovídat {1} předplatné obsažené v ID spravovaného disku.  |
|  Parametr InvalidParameter  |  Vlastní data v OSProfile musí být v kódování Base64 a s maximální délkou {0} znaků.  |
|  Parametr InvalidParameter  |  Název objektu {0} blob v{1}adrese URL musí končit příponou .  |
|  Parametr InvalidParameter  |  {0}' není platná předpona názvu objektu blob zachyceného v rozlišení VHD. Platná předpona odpovídá{1}regulárnímu výrazu ' .  |
|  Parametr InvalidParameter  |  Certifikáty nelze přidat do virtuálního počítače, pokud není zřízenagent virtuálního počítače.  |
|  Parametr InvalidParameter  |  Disk na {0} logické jednotce již existuje.  |
|  Parametr InvalidParameter  |  Nelze vytvořit virtuální ho virtuálního {0} počítače, protože požadovaná velikost není k dispozici v clusteru, kde je aktuálně přidělena sada dostupnosti. Dostupné velikosti jsou: {1}. Přečtěte si další informace o https://aka.ms/azure-resizevmstrategii pro změna velikosti virtuálních počítači na adrese .  |
|  Parametr InvalidParameter  |  Požadovaná velikost {0} virtuálního počítače není v aktuální oblasti k dispozici. Velikosti dostupné v aktuální oblasti {1}jsou: . Další informace o dostupných velikostech virtuálních https://aka.ms/azure-regionspočítačů v jednotlivých oblastech najdete na .  |
|  Parametr InvalidParameter  |  Požadovaná velikost {0} virtuálního počítače není v aktuální oblasti k dispozici. Další informace o dostupných velikostech virtuálních https://aka.ms/azure-regionspočítačů v jednotlivých oblastech najdete na .  |
|  Parametr InvalidParameter  |  Uživatelské jméno správce systému {0} Windows nesmí být delší než znaky, končit {1}tečkou(.) nebo obsahovat následující znaky: .  |
|  Parametr InvalidParameter  |  Název počítače se systémem {0} Windows nesmí být větší než počet znaků, nesmí být zcela číselný nebo obsahovat následující znaky: {1}.  |
|  ChybějícíMoveDependentResources  |  Požadavek přesunutí prostředků neobsahuje všechny závislé prostředky. Zkontrolujte podrobnosti o chybě chybějících ID prostředků.  |
|  MoveResourcesHaveInvalidState  |  Požadavek přesunout prostředky obsahuje virtuální servery, které jsou přidruženy k neplatným účtům úložiště. Zkontrolujte podrobnosti o těchto ID prostředků a názvy účtů s odkazem na úložiště.  |
|  MoveResourcesHavePendingOperations  |  Požadavek na přesun prostředků obsahuje prostředky, pro které čeká na operaci. Zkontrolujte podrobnosti o těchto ID prostředků. Opakujte operaci po dokončení čekajících operací.  |
|  MoveResourcesNotFound  |  Požadavek na přesun prostředků obsahuje prostředky, které nelze najít. Zkontrolujte podrobnosti o těchto ID prostředků.  |
|  Chyba operace NetworkingInternalOperationError  |  Neznámá chyba přidělení sítě.  |
|  Chyba operace NetworkingInternalOperationError  |  Neznámá chyba přidělení sítě  |
|  Chyba operace NetworkingInternalOperationError  |  Při zpracování profilu sítě virtuálního soudu došlo k vnitřní chybě.  |
|  NotFound  |  Sada {0} dostupnosti nebyla nalezena.  |
|  NotFound  |  Zdrojový virtuální{0}počítač ' ' zadaný v požadavku neexistuje v tomto umístění Azure.  |
|  NotFound  |  Tenant s {0} id nebyl nalezen.  |
|  NotFound  |  Obrázek {0} nebyl nalezen.  |
|  Není podporováno  |  Typ licence {0}je , ale {1} objekt blob obrázku není z místního prostředí.  |
|  Operace není povolena.  |  Skupinu {0} dostupnosti nelze odstranit. Před odstraněním sady dostupnosti se ujistěte, že neobsahuje žádný virtuální virtuální virtuální měna.  |
|  Operace není povolena.  |  Změna skladové položky dostupnosti ze zarovnané na klasické není povolena.  |
|  Operace není povolena.  |  Nelze změnit rozšíření ve virtuálním provozu, pokud virtuální hod není spuštěn.  |
|  Operace není povolena.  |  Akce Zachycení je podporovaná jenom ve virtuálním počítači s disky založenými na objektech blob. K vytvoření image ze spravovaného virtuálního počítače použijte api prostředků image image.  |
|  Operace není povolena.  |  Prostředek {0} nelze vytvořit z {1} image, dokud image byla úspěšně vytvořena.  |
|  Operace není povolena.  |  Aktualizace šifrováníNastavení není povoleno při přidělení virtuálního počítače, opakujte akci po přidělení virtuálního počítače  |
|  Operace není povolena.  |  Přidání spravovaného disku do virtuálního počítače s disky založenými na objektech blob není podporováno.  |
|  Operace není povolena.  |  Maximální počet datových disků, které mohou být připojeny {0}k virtuálnímu počítače této velikosti je .  |
|  Operace není povolena.  |  Přidání disku založeného na objektu blob do virtuálního počítače se spravovanými disky není podporováno.  |
|  Operace není povolena.  |  Operace{0}' ' není v{1}obrázku ' povolena, protože obrázek je označen k odstranění. Můžete pouze opakovat operaci Odstranit (nebo čekat na probíhající jeden k dokončení).  |
|  Operace není povolena.  |  Operace{0}" " není povolena{1}na virtuálním počítači , protože virtuální počítač je generalizovaný.  |
|  Operace není povolena.  |  Operace{0}' ' není povolena,{1}protože kolekce bodů obnovení ' je označena k odstranění.  |
|  Operace není povolena.  |  Operace{0}' ' není povolena{1}u rozšíření virtuálního počítače , protože je označena k odstranění. Můžete pouze opakovat operaci Odstranit (nebo čekat na probíhající jeden k dokončení).  |
|  Operace není povolena.  |  Operace{0}" " není povolena,{1}protože virtuální počítače "{2}jsou zřizovány pomocí obrázku " .  |
|  Operace není povolena.  |  Operace{0}' ' není povolena, protože{1}sada velikosti virtuálního{2}počítače ' ' právě používá obrázek .  |
|  Operace není povolena.  |  Operace{0}' ' není povolena{1}na virtuálním počítači ' , protože virtuální počítač je označen k odstranění. Můžete pouze opakovat operaci Odstranit (nebo čekat na probíhající jeden k dokončení).  |
|  Operace není povolena.  |  Operace{0}" ' není povolena{1}na virtuálním počítači , protože virtuální počítač je buď vyrovnaný, nebo označený k odále.  |
|  Operace není povolena.  |  Operace{0}' ' není povolena{1}na virtuálním počítači ' , protože je spuštěn virtuální počítač. V případě, že virtuální ho vypnete z hostovaného operačního systému, vypněte ho explicitně.  |
|  Operace není povolena.  |  Operace{0}" ' není povolena{1}na virtuálním počítači , protože virtuální počítač není vrácen.  |
|  Operace není povolena.  |  Operace{0}' ' není povolena{1}na virtuálním počítači , protože virtuální počítač má rozšíření '{2}' ve stavu selhání.  |
|  Operace není povolena.  |  Operace{0}' ' není povolena{1}na virtuálním počítači , protože probíhá jiná operace.  |
|  Operace není povolena.  |  Operace "{0}" vyžaduje generalizování virtuálního počítače .{1}  |
|  Operace není povolena.  |  Operace vyžaduje, aby byl virtuální virtuální ms spuštěn (nebo nastaven na spuštění).  |
|  Operace není povolena.  |  Disk s {0}velikostí GB, která {1}je menší než velikost GB odpovídajícího disku v bitové kopii, není povolen.  |
|  Operace není povolena.  |  Rozšíření sady měřítka virtuálních{0}vod pro obslužnou rutinu " " lze přidat pouze v době vytvoření sady měřítka virtuálního měn.  |
|  Operace není povolena.  |  Rozšíření sady měřítka virtuálního{0}měniče obslužné rutiny " lze odstranit pouze v době odstranění sady měřítka virtuálního měniče.  |
|  Operace není povolena.  |  Virtuální počítače{0}' ' již používá spravované disky.  |
|  Operace není povolena.  |  Virtuální ho{0}virtuálního telefonu " "{1}patří do sady dostupnosti "Classic" ". Aktualizujte sadu dostupnosti tak, aby používala skladovou položku Zarovnaná, a opakujte převod.  |
|  Operace není povolena.  |  Virtuální počítače vytvořené z image nemůže mít disky založené na objektu blob. Všechny disky musí být spravované disky.  |
|  Operace není povolena.  |  Operaci zachycení nelze dokončit, protože virtuální hod není generalizován.  |
|  Operace není povolena.  |  Operace správy na{0}virtuálním počítači ' ' jsou zakázány, protože disky virtuálních počítačů se převádějí na spravované disky.  |
|  Operace není povolena.  |  Probíhající operace mění stav napájení {0} virtuálního počítače na {1}. Prosím, {2} proveďte operaci po určité době.  |
|  Operace není povolena.  |  Nelze přidat nebo aktualizovat virtuální ho. Požadovaná velikost {0} virtuálního počítače nemusí být k dispozici ve stávající alokační jednotce. Přečtěte si další informace o https://aka.ms/azure-resizevmstrategii pro změna velikosti virtuálních počítači na adrese .  |
|  Operace není povolena.  |  Nelze změnit velikost virtuálního počítače, {0} protože požadovaná velikost není k dispozici v clusteru, kde je aktuálně přidělena sada dostupnosti. Dostupné velikosti jsou: {1}. Přečtěte si další informace o https://aka.ms/azure-resizevmstrategii pro změna velikosti virtuálních počítači na adrese .  |
|  Operace není povolena.  |  Nelze změnit velikost virtuálního počítače, {0} protože požadovaná velikost není k dispozici v clusteru, kde je aktuálně přidělen virtuální ho. Chcete-li změnit velikost {1} virtuálního počítače, prosím navrátit (to je zastavit operace na webu Azure Portal) a zkuste operaci změnit velikost znovu. Přečtěte si další informace o https://aka.ms/azure-resizevmstrategii pro změna velikosti virtuálních počítači na adrese .  |
|  Chyba klienta OSProvisioning  |  Zřizování operačního služby se nezdařilo pro virtuální hod ,{0}protože hostovaného operačního aktuálně zřizován.  |
|  Chyba klienta OSProvisioning  |  Zřizování operačního{0}spoje pro virtuální ho virtuálního virtuálního ms se nezdařilo. Podrobnosti {1} o chybě: Zkontrolujte, zda byl obrázek správně připraven (zobecněn). <ul><li>Pokyny pro Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  Chyba klienta OSProvisioning  |  Generování klíčů hostitele SSH se nezdařilo. Podrobnosti {0}o chybě: . Chcete-li tento problém vyřešit, ověřte, zda je agent Linuxsprávně nastaven. <ul><li>Pokyny si můžete ověřit na adrese:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  Chyba klienta OSProvisioning  |  Uživatelské jméno zadané pro virtuální počítač je pro tuto distribuci Linuxu neplatné. Podrobnosti {0}o chybě: .  |
|  OSProvisioningInternalError  |  Zřizování operačního systému se nezdařilo pro virtuální hod z{0}důvodu vnitřní chyby.  |
|  OSProvisioningTimedOut  |  Zřizování operačního{0}spoje pro virtuální virtuální mě nebylo dokončeno v přiděleném čase. Virtuální ho virtuálního ms může stále úspěšně dokončit zřizování. Zkontrolujte stav zřizování později.  |
|  OSProvisioningTimedOut  |  Zřizování operačního{0}spoje pro virtuální virtuální mě nebylo dokončeno v přiděleném čase. Virtuální ho virtuálního ms může stále úspěšně dokončit zřizování. Zkontrolujte stav zřizování později. Také se ujistěte, že obraz byl správně připraven (generalizován).   <ul><li>Pokyny pro Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Pokyny pro Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Zřizování operačního{0}spoje pro virtuální virtuální mě nebylo dokončeno v přiděleném čase. Agent hosta virtuálního zařízení však byl zjištěn spuštěn. To naznačuje, že hostovaný operační systém nebyl správně připraven k použití jako image virtuálního zařízení (s CreateOption=FromImage). Chcete-li tento problém vyřešit, použijte virtuální pevný disk jako je s CreateOption=Attach nebo jej připravte správně pro použití jako obrázek:   <ul><li>Pokyny pro Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Pokyny pro Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Požadovaná velikost virtuálního počítače není momentálně dostupná ve vybraném umístění.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Prostředek nelze v tuto chvíli aktualizovat z důvodu probíhající aktualizace platformy. Zkuste to prosím znovu později.  |
|  StorageAccountLimitation  |  Účet úložiště{0}' ' nepodporuje objekty BLOB stránky, které jsou nutné k vytvoření disků.  |
|  StorageAccountLimitation  |  Účet úložiště{0}' ' překročil přidělenou kvótu.  |
|  StorageAccountLocationMismatch  |  Účet úložiště nelze {0}vyřešit. Ujistěte se, že byla vytvořena prostřednictvím zprostředkovatele prostředků úložiště ve stejném umístění jako výpočetní prostředek.  |
|  StorageAccountNotFound  |  Účet {0} úložiště nebyl nalezen. Ujistěte se, že účet úložiště není odstraněn a patří do stejného umístění Azure jako virtuální počítač.  |
|  StorageAccountNotRecognized  |  Použijte účet úložiště spravovaný zprostředkovatelem prostředků úložiště. Použití {0} není podporováno.  |
|  StorageAccountOperationInternalError  |  Při přístupu k účtu {0}úložiště došlo k vnitřní chybě.  |
|  StorageAccountSubscriptionMismatch  |  Účet {0} úložiště nepatří k {1}předplatnému .  |
|  StorageAccountTooBusy  |  Účet úložiště{0}' ' je v současné době příliš zaneprázdněn. Zvažte použití jiného účtu.  |
|  StorageAccountTypeNotSupported  |  Disk {0} {1} používá účet úložiště objektů Blob. Opakujte akci s účtem úložiště pro obecné účely.  |
|  StorageAccountTypeNotSupported  |  Účet {0} úložiště {1} je typu. Diagnostika spouštění podporuje {2} typy účtů úložiště.  <ul><li>K této chybě dochází, pokud použijete účet úložiště premium pro diagnostiku spouštění. Další informace naleznete v tématu [Použití diagnostiky spouštění](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Předplatné není autorizováno.  |
|  TargetDiskBlobAlreadyExists  |  Objekt {0} Blob již existuje. Zadejte jiný identifikátor URI objektu BLOB{1}a vytvořte nový prázdný datový disk .  |
|  TargetDiskBlobAlreadyExists  |  Operace zachycení nemůže pokračovat, {0} protože objekt blob cílové bitové kopie již existuje a není nastaven příznak pro přepsání objektů BLOB virtuálního pevného disku. Odstraňte objekt blob nebo nastavte příznak tak, aby přepsal objekty BLOB virtuálního pevného disku a zopakujte akci.  |
|  TargetDiskBlobAlreadyExists  |  Operace zachycení nemůže pokračovat, {0} protože objekt blob cílové bitové kopie má aktivní zapůjčení.   |
|  TargetDiskBlobAlreadyExists  |  Objekt {0} Blob již existuje. Zadejte jiný identifikátor URI objektu{1}BLOB jako cíl pro disk .  |
|  TooManyVMRedeploymentRequests  |  Příliš mnoho žádostí o opětovné nasazení{0}byly přijaty pro virtuální počítače ' nebo virtuálnípočítače ve stejné dostupnosti s tímto virtuálním počítačem. Opakujte akci později.  |
|  Velikost vrozhraní VHDNeplatný  |  Zadaná hodnota {0} velikosti{1}disku pro {2} disk ' ' s objektem blob je neplatná. Velikost disku musí {3} {4}být mezi a .  |
|  Chyba komunikace vagentstatus  |  Virtuální mkér na{0}to, že nehlásí stav agenta virtuálního virtuálního mě nebo rozšíření. Ověřte, zda má virtuální počítač spuštěného agenta virtuálního počítače a můžete navázat odchozí připojení k úložišti Azure.  |
|  Vnitřní chyba v úložišti vúložišti v  |  Při komunikaci s úložištěm artefaktů došlo k chybě, aby se načetly podrobnosti o artefaktech virtuálního rozhraní.  |
|  Vnitřní chyba v úložišti vúložišti v  |  Při načítání dat artefaktů virtuálního rozhraní z úložiště artefaktů došlo k vnitřní chybě.  |
|  Chyba Nepřechodového operátora VMExtensionHandler  |  Obslužná{0}rutina '{1}' nahlásila chybu{2}pro rozšíření virtuálního zařízení ' ' s kódem chyby terminálu ' ' a chybovou zprávou: '{3}  |
|  VMExtensionManagementInternalError  |  Při zpracování rozšíření virtuálního{0}zařízení došlo k vnitřní chybě.  |
|  VMExtensionManagementInternalError  |  Při přípravě rozšíření virtuálního počítače došlo k několika chybám. Podrobnosti najdete v tématu zobrazení instance rozšíření virtuálního vod.  |
|  VMExtensionProvisioningError  |  Virtuální měkce při zpracování{0}rozšíření ohlásila chyba. Chybová zpráva: "{1}".  |
|  VMExtensionProvisioningError  |  Více rozšíření virtuálních počítačových služeb se nepodařilo zřídit na virtuálním počítači. Podrobnosti najdete v zobrazení instance rozšíření virtuálního data.  |
|  Časový čas poskytování vdonášku v  |  Zřizování rozšíření{0}virtuálního virtuálního času ' má časový rozsah. Instalace rozšíření může trvat příliš dlouho nebo nelze získat stav rozšíření.  |
|  VMMarketplaceInvalidInput  |  Vytvoření virtuálního počítače z image jiného než Marketplace nepotřebuje informace o plánu, odeberte informace o plánu v žádosti. Název disku operačního systému je {0}.  |
|  VMMarketplaceInvalidInput  |  Informace o nákupu se neshodují. Nelze nasadit z image Marketplace. Název disku operačního systému je {0}.  |
|  VMMarketplaceInvalidInput  |  Vytvoření virtuálního počítače z image Marketplace vyžaduje informace o plánu v požadavku. Název disku operačního systému je {0}.  |
|  VMNotFound  |  Virtuální ho{0}virtuálního souboru " " nebyl nalezen.  |
|  Obnovení nasazení virtuálních můe se nezdařilo.  |  Opětovné nasazení{0}virtuálního počítače se nezdařilo z důvodu vnitřní chyby. Opakujte akci později.  |
|  VMRedeploymentTimedOut  |  Přeřazení virtuálního{0}počítače v přiděleném čase nebylo dokončeno. To by mohlo skončit úspěšně v někdy. Jinak můžete požadavek zopakovat.  |
|  VMStartTimedOut  |  Virtuální měna "{0}se nespustila v přiděleném čase. Virtuální virtuální ms může stále úspěšně spustit. Zkontrolujte stav napájení později.  |


## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc, můžete kontaktovat odborníky azure na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
