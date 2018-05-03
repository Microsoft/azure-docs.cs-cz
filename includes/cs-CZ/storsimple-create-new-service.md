<!--author=alkohli last changed:01/14/2016-->


#### <a name="to-create-a-new-service"></a>Vytvoření nové služby
1. Pomocí přihlašovacích údajů k účtu Microsoft se přihlaste na portál Azure Classic na této adrese URL: [https://manage.windowsazure.com/](https://manage.windowsazure.com/).
2. Na portálu Azure Classic klikněte na **Nový** > **Data Services** > **StorSimple Manager** > **Rychlý start**.
3. Ve formuláři, který se zobrazí, proveďte následující akce:
   
   1. Zadejte jedinečný **Název** služby. Jedná se o popisný název, který můžete použít k identifikaci služby. Název může být tvořen 2 až 50 písmeny, číslicemi a spojovníky. Název musí začínat a končit písmenem nebo číslicí.
   2. Zadejte **Umístění** služby. Obvykle je dobré vybrat umístění, které je nejblíže geografickému regionu, kde chcete zařízení nasadit. Zvažte i následující okolnosti: 
      
      * Pokud máte v Azure existující úlohy, které chcete také nasadit se zařízením StorSimple, měli byste použít stejné datové centrum.
      * Služby StorSimple Manager a úložiště Azure mohou být ve dvou různých umístěních. V takovém případě je nutné vytvořit účty služeb StorSimple Manager a úložiště Azure odděleně. Pokud chcete vytvořit účet úložiště Azure, přejděte do služby Azure Storage na portálu Azure Classic a postupujte způsobem popsaným v článku [Vytvoření účtu služby Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account). Po přihlášení k tomuto účtu pomocí kroků popsaných v článku [Konfigurace nového účtu úložiště pro službu](../articles/storsimple/storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service) účet přidejte do služby StorSimple Manager.
   3. V rozevíracím seznamu **Předplatné** vyberte předplatné. Předplatné je propojeno s vaším fakturačním účtem. Toto pole není dostupné, pokud máte pouze jedno předplatné.
   4. Výběrem možnosti **Vytvořit nový účet úložiště** můžete vytvořit účet úložiště automaticky se službou. Takový účet úložiště bude mít speciální název, například storsimplebwv8c6dcnf. Pokud potřebujete mít data v jiném umístění, zaškrtnutí políčka zrušte. 
   5. Kliknutím na **Vytvořit StorSimple Manager** vytvořte službu.
   
   ![Vytvoření služby StorSimple Manager](./media/storsimple-create-new-service/HCS_CreateAService-include.png)
   
   Budete přesměrováni na cílovou stránku **služby**. Vytvoření služby může trvat několik minut. Po úspěšném vytvoření služby budete odpovídajícím způsobem upozorněni a stav služby se změní na **Aktivní**.
   
   ![Vytvoření služby](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

![Dostupné video](./media/storsimple-create-new-service/Video_icon.png)**Dostupné video**

Pokud si chcete přehrát video, které ukazuje, jak vytvořit novou službu StorSimple Manager, klikněte [sem](https://azure.microsoft.com/documentation/videos/create-a-storsimple-manager-service/).

