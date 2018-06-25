<!--author=alkohli last changed:02/10/2017-->


#### <a name="to-create-a-new-service"></a>Vytvoření nové služby

1. Pomocí přihlašovacích údajů účtu Microsoft se přihlaste na webu [Azure Portal](https://portal.azure.com/).

2. Na webu Azure Portal klikněte na **Vytvořit prostředek** a na Marketplace potom klikněte na **Zobrazit vše**.

    ![Vytvoření Správce zařízení StorSimple](./media/storsimple-8000-create-new-service/createssdevman1.png)

    Vyhledejte _Fyzické zařízení StorSimple_. Vyberte a klikněte na **Řada fyzických zařízení StorSimple** a pak klikněte na **Vytvořit**. Případně můžete na webu Azure Portal kliknout na **+** a pak v části **Úložiště** kliknout na **Řada fyzických zařízení StorSimple**.

    ![Vytvoření Správce zařízení StorSimple](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. V okně **Správce zařízení StorSimple** proveďte následující kroky:
   
   1. Zadejte pro službu jedinečný **Název prostředku**. Tento název je popisný název, který můžete použít k identifikaci služby. Název může být tvořen 2 až 50 písmeny, číslicemi a spojovníky. Název musí začínat a končit písmenem nebo číslicí.

   2. V rozevíracím seznamu **Předplatné** vyberte předplatné. Předplatné je propojeno s vaším fakturačním účtem. Toto pole není dostupné, pokud máte pouze jedno předplatné.

   3. V části **Skupina prostředků** vyberte možnost **Použít existující** nebo **Vytvořit novou** skupinu. Další informace najdete v tématu [Skupiny prostředků Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).
   
   4. Zadejte **Umístění** služby. Obvykle platí, že byste měli zvolit umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit. Měli byste zvážit i následující aspekty: 
      
      * Pokud máte v Azure existující úlohy, které chcete také nasadit se zařízením StorSimple, měli byste použít stejné datové centrum.
      * Služba Správce zařízení StorSimple a úložiště Azure můžou být ve dvou různých umístěních. V takovém případě je nutné vytvořit Správce zařízení StorSimple a účet úložiště Azure odděleně. Pokud chcete vytvořit účet úložiště Azure, přejděte na webu Azure Portal do služby Azure Storage a postupujte podle kroků popsaných v tématu [Vytvoření účtu služby Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account). Po vytvoření účtu jej přidejte do služby Správce zařízení StorSimple pomocí kroků popsaných v tématu [Konfigurace nového účtu úložiště pro službu](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service).

   5. Výběrem možnosti **Vytvořit nový účet úložiště** můžete vytvořit účet úložiště automaticky se službou. Zadejte název tohoto účtu úložiště. Pokud potřebujete mít data v jiném umístění, zaškrtnutí políčka zrušte.

   6. Pokud chcete mít na řídicím panelu rychlý odkaz na tuto službu, zaškrtněte políčko **Připnout na řídicí panel**.
      
   7. Kliknutím na **Vytvořit** vytvořte Správce zařízení StorSimple.

       ![Vytvoření Správce zařízení StorSimple](./media/storsimple-8000-create-new-service/createssdevman2.png)
   
Vytvoření služby trvá několik minut. Po úspěšném vytvoření služby se zobrazí oznámení a otevře se okno nové služby.
   
![Vytvoření Správce zařízení StorSimple](./media/storsimple-8000-create-new-service/createssdevman5.png)


