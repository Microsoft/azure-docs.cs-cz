<!--author=SharS last changed: 12/01/15-->

### <a name="step-1-authorize-a-device-to-change-the-service-data-encryption-key-in-the-azure-classic-portal"></a>Krok 1: Autorizace zařízení, které chcete změnit šifrovacího klíče dat služby v portálu Azure classic
Obvykle Správce zařízení bude požadovat, aby správce služeb autorizace zařízení, které chcete změnit šifrovací klíče dat služby. Správce služeb bude potom budete autorizovat zařízení změňte klíč.

Tento krok se provádí na portálu Azure classic. Správce služeb můžete vybrat zařízení ze seznamu zobrazených zařízení, které jsou způsobilé k autorizaci. Zařízení je pak oprávnění ke spuštění procesu změny klíče šifrování dat služby.

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Zařízení, která lze udělit oprávnění k změnit šifrovací klíče dat služby?
Zařízení musí splňovat následující kritéria, než může být oprávnění k inicializaci služby dat šifrování klíče změny:

* Zařízení musí být online způsobilé k autorizaci změny klíče šifrování dat služby.
* Znovu po 30 minutách může autorizovat do stejného zařízení, pokud změny klíče nebyla inicializována.
* Jiné zařízení, můžete povolit za předpokladu, že změny klíče nebyla inicializována dříve oprávněným zařízením. Po autorizoval nové zařízení stará zařízení nelze zahájit změnu.
* Zařízení nejde autorizovat, když probíhá výměna šifrovacího klíče dat služby.
* Zařízení můžete povolit, pokud některá zařízení zaregistrovaná ve službě service mít převracet šifrování, jiné mají není. V takových případech způsobilých zařízeních jsou ty, které byly dokončeny šifrovacího klíče dat služby změnit.

> [!NOTE]
> Na portálu Azure classic nejsou zobrazeny virtuální zařízení StorSimple v seznamu zařízení, která může získat oprávnění ke spuštění změny klíče.
> 
> 

Proveďte následující kroky a vyberte autorizace zařízení zahájíte změny klíče šifrování dat služby.

#### <a name="to-authorize-a-device-to-change-the-key"></a>Autorizace zařízení a změňte klíč
1. Na stránce řídicího panelu služby klikněte na **šifrovacího klíče dat služby změnu**.
   
    ![Šifrovací klíč služby změn](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)
2. V **šifrovacího klíče dat služby změnu** dialogovém okně vyberte a autorizovat zařízení zahájíte změny klíče šifrování dat služby. Rozevíracím seznamu má všech způsobilých zařízeních, které lze udělit oprávnění.
3. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png).

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Krok 2: Použití Windows Powershellu pro StorSimple zahájíte změny klíče šifrování dat služby
Tento krok se provádí v prostředí Windows PowerShell pro rozhraní StorSimple na autorizované zařízení StorSimple.

> [!NOTE]
> Žádné operace lze provést na portálu Azure classic služby StorSimple Manager, dokud se nedokončí výměny klíčů.
> 
> 

Pokud používáte konzole sériového portu zařízení pro připojení k rozhraní Windows PowerShell, proveďte následující kroky.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Zahajte změnu klíče šifrování dat služby
1. Výběrem možnosti 1 přihlaste s úplným přístupem.
2. Na příkazovém řádku zadejte:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Po úspěšném dokončení rutiny, zobrazí se nové šifrovacího klíče dat služby. Zkopírujte a uložte tento klíč pro použití v kroku 3 tohoto procesu. Tento klíč se používá k aktualizaci všech zbývajících zařízení registrovaný ve službě StorSimple Manager.
   
   > [!NOTE]
   > Tento proces musí zahájit v rámci čtyři hodiny autorizace zařízení StorSimple.
   > 
   > 
   
   Tento nový klíč je odeslaný do služby k vloží do všech zařízení, která jsou zaregistrovaná ve službě service. Na řídicím panelu služby se potom zobrazí výstrahu. Službu vypne všechny operace na registrovaná zařízení a pak muset aktualizovat šifrovacího klíče dat služby na jiných zařízení Správce zařízení. Ale nesmí být přerušeny vstupně-výstupních operací (odesílání dat do cloudu hostitelů).
   
   Pokud máte jedno zařízení zaregistrován ke službě, proces výměny je nyní dokončen a můžete přejít na další krok. Pokud máte více zařízení zaregistrován ke službě, pokračujte krokem 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Krok 3: Aktualizace šifrovacího klíče dat služby na jiná zařízení StorSimple
Tyto kroky je potřeba provést v prostředí Windows PowerShell rozhraní zařízení StorSimple, pokud máte více zařízení zaregistrován ke službě StorSimple Manager. Klíč, který jste získali v kroku 2: použití Windows Powershellu pro StorSimple zahájíte změny klíče šifrování dat služby musíte použít k aktualizaci všech zbývajících zařízení StorSimple registrovaný ve službě StorSimple Manager.

Proveďte následující kroky k aktualizaci šifrování dat služby ve vašem zařízení.

#### <a name="to-update-the-service-data-encryption-key"></a>Chcete-li aktualizovat šifrovacího klíče dat služby
1. Pomocí Windows Powershellu pro StorSimple pro připojení ke konzole. Výběrem možnosti 1 přihlaste s úplným přístupem.
2. Na příkazovém řádku zadejte:
   
    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Zadejte šifrovací klíč dat služby, který jste získali v [krok 2: použití Windows Powershellu pro StorSimple zahájíte změny klíče šifrování dat služby](#to-initiate-the-service-data-encryption-key-change).

