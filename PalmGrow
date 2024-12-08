package project;

import javafx.application.Application;
import javafx.beans.property.SimpleStringProperty;
import javafx.beans.property.StringProperty;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.chart.LineChart;
import javafx.scene.chart.NumberAxis;
import javafx.scene.chart.XYChart;
import javafx.scene.control.*;
import javafx.scene.layout.*;
import javafx.stage.Stage;

public class MainFix extends Application {
    private TableView<HarvestRecord> harvestTable;
    private TableView<PaymentRecord> paymentTable;
    private ObservableList<HarvestRecord> harvestRecords = FXCollections.observableArrayList();
    private ObservableList<PaymentRecord> paymentRecords = FXCollections.observableArrayList();
    private Stage primaryStage;
    private String selectedFactory = "Factory A";
    private final java.util.Map<String, Double> factoryPrices = new java.util.HashMap<>();
    private Label currentPriceLabel;
    private LineChart<Number, Number> priceChart;
    private Label selectedFactoryLabel;
    private Label pricePerKgLabel;

    @Override
    public void start(Stage primaryStage) {
        this.primaryStage = primaryStage;
        initializeFactoryPrices();
        primaryStage.setTitle("Palm Grow Management System");
        Scene loginScene = createLoginScene();
        primaryStage.setScene(loginScene);
        primaryStage.show();
    }

    private void initializeFactoryPrices() {
        factoryPrices.put("Factory A", 3000.0);
        factoryPrices.put("Factory B", 3500.0);
        factoryPrices.put("Factory C", 2500.0);
    }

    private Scene createLoginScene() {
        VBox loginLayout = new VBox(20);
        loginLayout.setAlignment(Pos.CENTER);
        loginLayout.setPadding(new Insets(40));

        Label titleLabel = new Label("Welcome to PalmGrow");
        titleLabel.setStyle("-fx-font-size: 24px; -fx-font-weight: bold;");

        Label subtitleLabel = new Label("Please Login to Continue");
        subtitleLabel.setStyle("-fx-font-size: 14px;");

        TextField usernameField = new TextField();
        usernameField.setPromptText("Username");
        usernameField.setPrefWidth(300);

        PasswordField passwordField = new PasswordField();
        passwordField.setPromptText("Password");
        passwordField.setPrefWidth(300);

        Button loginButton = new Button("Login");
        loginButton.setStyle("-fx-background-color: #2ECC71; -fx-text-fill: white;");
        loginButton.setPrefWidth(300);

        loginButton.setOnAction(e -> {
            Scene mainScene = createMainScene();
            primaryStage.setScene(mainScene);
        });

        loginLayout.getChildren().addAll(
                titleLabel,
                subtitleLabel,
                usernameField,
                passwordField,
                loginButton
        );

        return new Scene(loginLayout, 800, 600);
    }

    private Scene createMainScene() {
        TabPane tabPane = new TabPane();

        Tab harvestTab = new Tab("Harvest Management");
        harvestTab.setContent(createHarvestContent());
        harvestTab.setClosable(false);

        Tab paymentTab = new Tab("Payment Processing");
        paymentTab.setContent(createPaymentContent());
        paymentTab.setClosable(false);

        Tab priceChartTab = new Tab("Price Chart");
        priceChartTab.setContent(createPriceChartContent());
        priceChartTab.setClosable(false);

        Tab learningModuleTab = new Tab("Learning Module");
        learningModuleTab.setContent(createLearningModuleContent());
        learningModuleTab.setClosable(false);

        tabPane.getTabs().addAll(harvestTab, paymentTab, priceChartTab, learningModuleTab);
        return new Scene(tabPane, 1024, 768);
    }

    private VBox createHarvestContent() {
        VBox content = new VBox(20);
        content.setPadding(new Insets(20));

        Label titleLabel = new Label("Harvest Recording");
        titleLabel.setStyle("-fx-font-size: 20px; -fx-font-weight: bold;");

        GridPane inputGrid = new GridPane();
        inputGrid.setHgap(10);
        inputGrid.setVgap(10);
        inputGrid.setPadding(new Insets(20));

        DatePicker harvestDate = new DatePicker();
        TextField quantityField = new TextField();
        quantityField.setPromptText("Quantity (kg)");

        selectedFactoryLabel = new Label("Selected Factory: " + selectedFactory);
        pricePerKgLabel = new Label(String.format("Current Price: Rp%.0f per kg", factoryPrices.get(selectedFactory)));

        Button recordButton = new Button("Record Harvest");
        recordButton.setStyle("-fx-background-color: #2ECC71; -fx-text-fill: white;");

        inputGrid.add(new Label("Harvest Date:"), 0, 0);
        inputGrid.add(harvestDate, 1, 0);
        inputGrid.add(new Label("Quantity (kg):"), 0, 1);
        inputGrid.add(quantityField, 1, 1);
        inputGrid.add(selectedFactoryLabel, 0, 2);
        inputGrid.add(pricePerKgLabel, 1, 2);
        inputGrid.add(recordButton, 1, 3);

        harvestTable = new TableView<>();

        TableColumn<HarvestRecord, String> dateCol = new TableColumn<>("Harvest Date");
        dateCol.setCellValueFactory(cellData -> cellData.getValue().dateProperty());
        dateCol.setPrefWidth(150);

        TableColumn<HarvestRecord, String> quantityCol = new TableColumn<>("Quantity (kg)");
        quantityCol.setCellValueFactory(cellData -> cellData.getValue().quantityProperty());
        quantityCol.setPrefWidth(150);

        TableColumn<HarvestRecord, String> factoryCol = new TableColumn<>("Factory");
        factoryCol.setCellValueFactory(cellData -> cellData.getValue().factoryProperty());
        factoryCol.setPrefWidth(150);

        TableColumn<HarvestRecord, String> statusCol = new TableColumn<>("Status");
        statusCol.setCellValueFactory(cellData -> cellData.getValue().statusProperty());
        statusCol.setPrefWidth(150);

        harvestTable.getColumns().addAll(dateCol, quantityCol, factoryCol, statusCol);
        harvestTable.setItems(harvestRecords);

        recordButton.setOnAction(e -> {
            if (harvestDate.getValue() != null && !quantityField.getText().isEmpty()) {
                try {
                    double quantity = Double.parseDouble(quantityField.getText());
                    double price = factoryPrices.get(selectedFactory);
                    double totalPayment = quantity * price;

                    harvestRecords.add(new HarvestRecord(
                            harvestDate.getValue().toString(),
                            quantityField.getText(),
                            selectedFactory,
                            "Recorded"
                    ));

                    paymentRecords.add(new PaymentRecord(
                            harvestDate.getValue().toString(),
                            String.format("%.0f", totalPayment),
                            selectedFactory,
                            "Processed"
                    ));

                    harvestDate.setValue(null);
                    quantityField.clear();

                    showConfirmation("Success", String.format("Harvest recorded and payment of Rp%.0f processed", totalPayment));
                } catch (NumberFormatException ex) {
                    showError("Invalid Input", "Please enter a valid number for quantity.");
                }
            } else {
                showError("Missing Information", "Please fill in all fields.");
            }
        });

        content.getChildren().addAll(titleLabel, inputGrid, new Label("Harvest Records"), harvestTable);
        return content;
    }

    private VBox createPaymentContent() {
        VBox content = new VBox(20);
        content.setPadding(new Insets(20));

        Label titleLabel = new Label("Payment Processing");
        titleLabel.setStyle("-fx-font-size: 20px; -fx-font-weight: bold;");

        paymentTable = new TableView<>();

        TableColumn<PaymentRecord, String> dateCol = new TableColumn<>("Payment Date");
        dateCol.setCellValueFactory(cellData -> cellData.getValue().dateProperty());
        dateCol.setPrefWidth(150);

        TableColumn<PaymentRecord, String> amountCol = new TableColumn<>("Amount (Rp)");
        amountCol.setCellValueFactory(cellData -> cellData.getValue().amountProperty());
        amountCol.setPrefWidth(150);

        TableColumn<PaymentRecord, String> factoryCol = new TableColumn<>("Factory");
        factoryCol.setCellValueFactory(cellData -> cellData.getValue().factoryProperty());
        factoryCol.setPrefWidth(150);

        TableColumn<PaymentRecord, String> statusCol = new TableColumn<>("Status");
        statusCol.setCellValueFactory(cellData -> cellData.getValue().statusProperty());
        statusCol.setPrefWidth(150);

        paymentTable.getColumns().addAll(dateCol, amountCol, factoryCol, statusCol);
        paymentTable.setItems(paymentRecords);

        content.getChildren().addAll(titleLabel, paymentTable);
        return content;
    }

    private VBox createLearningModuleContent() {
        VBox content = new VBox(20);
        content.setPadding(new Insets(20));

        Label titleLabel = new Label("Learning Module");
        titleLabel.setStyle("-fx-font-size: 20px; -fx-font-weight: bold;");

        ListView<String> moduleListView = new ListView<>();
        ObservableList<String> modules = FXCollections.observableArrayList(
                "How to Manage Palm Oil Plantation",
                "Fertilization Techniques",
                "Harvesting Best Practices",
                "Pest Control Methods"
        );
        moduleListView.setItems(modules);

        TextArea articleDetails = new TextArea();
        articleDetails.setEditable(false);
        articleDetails.setWrapText(true);

        moduleListView.getSelectionModel().selectedItemProperty().addListener((obs, oldVal, newVal) -> {
            if (newVal != null) {
                switch (newVal) {
                    case "How to Manage Palm Oil Plantation":
                        articleDetails.setText("How to Manage Palm Oil Plantation\r\n" + 
                        		"\r\n" + 
                        		"Palm oil plantations play a significant role in the global agricultural economy, supplying raw materials for food products, cosmetics, and biofuels. Proper management of palm oil plantations is crucial to maximize yield, ensure sustainability, and meet international standards. This article provides a comprehensive guide on managing palm oil plantations effectively.\r\n" + 
                        		"\r\n" + 
                        		"1. Planning and Land Preparation\r\n" + 
                        		"Before establishing a palm oil plantation, thorough planning is necessary to ensure long-term success.\r\n" + 
                        		"\r\n" + 
                        		"Site Selection: Choose land with optimal soil conditions, good drainage, and a climate suitable for palm oil cultivation (tropical regions with high rainfall).\r\n" + 
                        		"Land Preparation: Clear the land carefully to minimize environmental impact. Employ practices like terracing in hilly areas to prevent erosion.\r\n" + 
                        		"Soil Testing: Conduct soil tests to determine nutrient levels and pH, and apply corrective measures such as liming or fertilization.\r\n" + 
                        		"2. Planting and Nursery Management\r\n" + 
                        		"Quality planting materials and careful nursery management are critical for establishing a productive plantation.\r\n" + 
                        		"\r\n" + 
                        		"Seed Selection: Choose high-yield hybrid seeds with resistance to diseases.\r\n" + 
                        		"Nursery Setup: Prepare a nursery with adequate irrigation and shade. Seedlings should be nurtured for 12–14 months before transplanting.\r\n" + 
                        		"Planting Layout: Adopt proper spacing (8–9 meters apart) to ensure optimal growth and light penetration.\r\n" + 
                        		"3. Crop Maintenance\r\n" + 
                        		"Effective maintenance practices help improve yields and reduce operational costs.\r\n" + 
                        		"\r\n" + 
                        		"Fertilization: Apply balanced fertilizers based on soil tests. Key nutrients include nitrogen, phosphorus, potassium, and magnesium.\r\n" + 
                        		"Weed Management: Use manual, mechanical, or chemical methods to control weeds, ensuring minimal competition for nutrients.\r\n" + 
                        		"Pest and Disease Control: Monitor for pests like rhinoceros beetles and diseases like Ganoderma. Implement integrated pest management (IPM) practices.\r\n" + 
                        		"Pruning: Regularly prune dead or old fronds to improve air circulation and reduce disease risk.\r\n" + 
                        		"4. Harvesting and Yield Optimization\r\n" + 
                        		"Efficient harvesting techniques are essential for maximizing profits.\r\n" + 
                        		"\r\n" + 
                        		"Harvest Timing: Harvest fruit bunches when they are fully ripe, indicated by loose fruitlets on the ground.\r\n" + 
                        		"Harvesting Tools: Use tools like chisels or sickles to minimize damage to the trees.\r\n" + 
                        		"Post-Harvest Handling: Transport harvested fruits to the processing facility promptly to avoid oil degradation.\r\n" + 
                        		"5. Sustainability Practices\r\n" + 
                        		"Sustainable practices are crucial to address environmental concerns and meet certification standards.\r\n" + 
                        		"\r\n" + 
                        		"Zero-Burning Policy: Avoid burning vegetation during land clearing to reduce carbon emissions.\r\n" + 
                        		"Soil and Water Conservation: Use cover crops, construct contour trenches, and maintain riparian zones to protect soil and water resources.\r\n" + 
                        		"Waste Management: Recycle palm oil mill effluent (POME) and other byproducts for composting or energy generation.\r\n" + 
                        		"Certification: Obtain certifications like RSPO (Roundtable on Sustainable Palm Oil) to demonstrate commitment to sustainable practices.\r\n" + 
                        		"6. Financial and Operational Management\r\n" + 
                        		"Managing finances and operations efficiently ensures the plantation's profitability.\r\n" + 
                        		"\r\n" + 
                        		"Budgeting: Develop a comprehensive budget covering establishment, maintenance, and operational costs.\r\n" + 
                        		"Record Keeping: Maintain records of plantation activities, yields, and expenditures for analysis and decision-making.\r\n" + 
                        		"Staff Training: Train workers on best practices in planting, harvesting, and maintenance.\r\n" + 
                        		"Technology Adoption: Leverage modern technologies such as drones for monitoring, GIS for mapping, and mechanized equipment to increase efficiency.\r\n" + 
                        		"Conclusion\r\n" + 
                        		"Managing a palm oil plantation requires a balance between productivity, sustainability, and profitability. By implementing best practices in land preparation, crop maintenance, harvesting, and sustainability, plantation owners can maximize yields while minimizing environmental impact. In an era where global consumers prioritize sustainable products, adopting responsible practices is not just beneficial—it is essential for long-term success.");
                        break;
                    case "Fertilization Techniques":
                        articleDetails.setText("Fertilization Techniques\r\n" + 
                        		"\r\n" + 
                        		"Fertilization is an essential practice in palm oil plantations, ensuring that plants receive the nutrients needed for optimal growth and yield. Proper fertilization techniques improve soil health, enhance productivity, and support sustainable farming practices. Below are the key aspects of effective fertilization.\r\n" + 
                        		"\r\n" + 
                        		"1. Importance of Fertilization\r\n" + 
                        		"Palm oil plants require both macronutrients and micronutrients for healthy growth:\r\n" + 
                        		"\r\n" + 
                        		"Nitrogen (N): Promotes leaf and stem growth.\r\n" + 
                        		"Phosphorus (P): Supports root and flower development.\r\n" + 
                        		"Potassium (K): Improves fruit quality and yield.\r\n" + 
                        		"Magnesium (Mg): Enhances photosynthesis.\r\n" + 
                        		"Micronutrients: Elements like boron (B) and zinc (Zn) are vital for metabolic functions.\r\n" + 
                        		"2. Types of Fertilizers\r\n" + 
                        		"Organic Fertilizers\r\n" + 
                        		"Organic options such as compost, animal manure, and processed palm fronds enrich the soil with natural nutrients while improving its structure.\r\n" + 
                        		"\r\n" + 
                        		"Chemical Fertilizers\r\n" + 
                        		"Chemical fertilizers provide specific nutrients in precise amounts. Common types include:\r\n" + 
                        		"\r\n" + 
                        		"Urea: Supplies nitrogen.\r\n" + 
                        		"TSP/SP-36: Provides phosphorus.\r\n" + 
                        		"KCl: Source of potassium.\r\n" + 
                        		"Kieserite: Supplies magnesium and sulfur.\r\n" + 
                        		"3. Fertilizer Application Techniques\r\n" + 
                        		"Broadcasting\r\n" + 
                        		"Fertilizer is evenly spread around the plants. This method is efficient for large plantations.\r\n" + 
                        		"\r\n" + 
                        		"Placement\r\n" + 
                        		"Fertilizer is applied in trenches or holes near the plant's root zone, ensuring direct nutrient absorption.\r\n" + 
                        		"\r\n" + 
                        		"Foliar Application\r\n" + 
                        		"Liquid fertilizer is sprayed on the leaves, quickly addressing nutrient deficiencies.\r\n" + 
                        		"\r\n" + 
                        		"4. Timing and Frequency\r\n" + 
                        		"Frequency: Fertilizer is applied 2–4 times annually.\r\n" + 
                        		"Season: Best done during the rainy season to help nutrients penetrate the soil.\r\n" + 
                        		"Growth Stage:\r\n" + 
                        		"Young plants: Focus on nitrogen and phosphorus for root and leaf development.\r\n" + 
                        		"Mature plants: Prioritize potassium and magnesium for fruit production.\r\n" + 
                        		"5. Soil and Leaf Analysis\r\n" + 
                        		"Conduct soil and leaf tests to determine the specific nutrient needs of the plants. This ensures balanced fertilization and prevents overuse, which could harm the environment.\r\n" + 
                        		"\r\n" + 
                        		"6. Sustainable Practices\r\n" + 
                        		"Recycle Waste: Use palm oil byproducts like empty fruit bunches as organic mulch.\r\n" + 
                        		"Precision Fertilization: Employ modern tools such as GPS and drones for efficient application.\r\n" + 
                        		"Protect the Environment: Avoid overuse of chemical fertilizers to prevent soil and water pollution.\r\n" + 
                        		"Conclusion\r\n" + 
                        		"Effective fertilization techniques are crucial for the success of palm oil plantations. By understanding the plants’ nutritional needs, selecting appropriate fertilizers, and applying them correctly, plantation owners can boost yields while promoting sustainability.");
                        break;
                    case "Harvesting Best Practices":
                        articleDetails.setText("Harvesting Best Practices\r\n" + 
                        		"\r\n" + 
                        		"Harvesting is a critical phase in palm oil plantation management, as it directly impacts oil quality, yield, and overall profitability. Employing the best practices ensures that fresh fruit bunches (FFBs) are harvested at the right time and handled properly to maintain their quality. Here are key practices for efficient and effective harvesting.\r\n" + 
                        		"\r\n" + 
                        		"1. Determining the Right Harvest Time\r\n" + 
                        		"Harvest timing significantly affects oil yield and quality.\r\n" + 
                        		"\r\n" + 
                        		"Indicators of Ripeness:\r\n" + 
                        		"Loose fruitlets on the ground (5–10 per bunch).\r\n" + 
                        		"Bright orange-red color on the fruit.\r\n" + 
                        		"Slight detachment of fruit from the bunch when touched.\r\n" + 
                        		"Harvesting Frequency:\r\n" + 
                        		"Mature plantations: Every 7–10 days.\r\n" + 
                        		"Younger plantations: Less frequent depending on fruit maturity.\r\n" + 
                        		"2. Tools and Equipment\r\n" + 
                        		"Using proper tools reduces damage to trees and improves worker efficiency.\r\n" + 
                        		"\r\n" + 
                        		"Harvesting Tools:\r\n" + 
                        		"Chisel: For young palms with shorter heights.\r\n" + 
                        		"Sickle: For taller palms using an extendable pole.\r\n" + 
                        		"Long-Reach Cutters: Mechanized options for high-efficiency harvesting.\r\n" + 
                        		"Transport Equipment: Use wheelbarrows, carts, or motorized loaders to move harvested bunches quickly to collection points.\r\n" + 
                        		"3. Harvesting Process\r\n" + 
                        		"Cutting the Bunch:\r\n" + 
                        		"\r\n" + 
                        		"Ensure a clean cut of the stalk to prevent damage to the tree.\r\n" + 
                        		"Avoid cutting unripe bunches to prevent reduced oil content.\r\n" + 
                        		"Collection of Loose Fruits:\r\n" + 
                        		"\r\n" + 
                        		"Gather loose fruits from the ground after each harvest to maximize yield.\r\n" + 
                        		"Immediate Transport:\r\n" + 
                        		"\r\n" + 
                        		"Quickly move FFBs to the processing facility or collection center to avoid oil deterioration caused by fermentation.\r\n" + 
                        		"4. Minimizing Losses\r\n" + 
                        		"Efficient harvesting reduces losses and maximizes productivity.\r\n" + 
                        		"\r\n" + 
                        		"Avoid Overripe Fruits: Overripe fruits lead to higher free fatty acid (FFA) levels, reducing oil quality.\r\n" + 
                        		"Minimize Bruising: Handle bunches carefully to avoid damaging the fruit, which can lead to spoilage.\r\n" + 
                        		"Monitor Worker Performance: Ensure workers follow proper techniques and meet quality standards.\r\n" + 
                        		"5. Worker Training\r\n" + 
                        		"Proper training ensures consistency and efficiency during harvesting.\r\n" + 
                        		"\r\n" + 
                        		"Train Workers: Educate workers on identifying ripeness, handling tools, and minimizing losses.\r\n" + 
                        		"Provide Safety Gear: Equip workers with gloves, helmets, and protective footwear to ensure safety.\r\n" + 
                        		"6. Sustainable Practices\r\n" + 
                        		"Adopting sustainable practices during harvesting benefits both the environment and long-term productivity.\r\n" + 
                        		"\r\n" + 
                        		"Zero-Waste Policy: Use empty fruit bunches (EFBs) as organic mulch or for composting.\r\n" + 
                        		"Eco-Friendly Transport: Reduce carbon footprint by optimizing transportation routes and using energy-efficient vehicles.\r\n" + 
                        		"7. Monitoring and Record Keeping\r\n" + 
                        		"Maintain detailed records of harvesting activities for analysis and improvement.\r\n" + 
                        		"\r\n" + 
                        		"Track harvested quantities, worker efficiency, and losses.\r\n" + 
                        		"Use digital tools or apps for real-time data collection and monitoring.\r\n" + 
                        		"Conclusion\r\n" + 
                        		"Following harvesting best practices is vital for maintaining high-quality palm oil production while minimizing losses. With proper timing, tools, worker training, and sustainability efforts, plantation managers can achieve better yields and enhance profitability.");
                        break;
                    case "Pest Control Methods":
                        articleDetails.setText("Pest Control Methods\r\n" + 
                        		"\r\n" + 
                        		"Pests pose significant challenges in palm oil plantations, affecting plant health, reducing yields, and increasing management costs. Implementing effective pest control methods helps protect the plantation and ensures sustainable production. Here are key pest control strategies for palm oil plantations.\r\n" + 
                        		"\r\n" + 
                        		"1. Identifying Common Pests in Palm Oil Plantations\r\n" + 
                        		"Before applying control methods, it's essential to identify the pests causing damage. Common pests include:\r\n" + 
                        		"\r\n" + 
                        		"Rhinoceros Beetle (Oryctes rhinoceros): Damages young palms by boring into the crown.\r\n" + 
                        		"Bagworm (Metisa plana): Defoliates palm leaves, reducing photosynthesis.\r\n" + 
                        		"Nettle Caterpillar (Setora nitens): Strips foliage, weakening the plant.\r\n" + 
                        		"Rats: Feed on fruits and cause significant yield losses.\r\n" + 
                        		"2. Integrated Pest Management (IPM)\r\n" + 
                        		"IPM is a holistic approach that combines multiple pest control methods to minimize damage while reducing environmental impact.\r\n" + 
                        		"\r\n" + 
                        		"a. Biological Control\r\n" + 
                        		"Utilize natural predators and beneficial organisms to control pests.\r\n" + 
                        		"\r\n" + 
                        		"Natural Predators: Introduce predator species such as birds, wasps, or ants to reduce pest populations.\r\n" + 
                        		"Pathogens: Use biological agents like Bacillus thuringiensis (Bt) to target bagworms and caterpillars.\r\n" + 
                        		"Parasitic Wasps: Release species that attack pest larvae.\r\n" + 
                        		"b. Cultural Practices\r\n" + 
                        		"Modify farming practices to prevent pest infestations.\r\n" + 
                        		"\r\n" + 
                        		"Sanitation: Regularly remove breeding sites such as decaying palm trunks to control rhinoceros beetles.\r\n" + 
                        		"Planting Cover Crops: Use ground cover plants to reduce pest habitats and improve soil health.\r\n" + 
                        		"Pruning: Remove dead fronds and old fruit bunches to discourage pests from settling.\r\n" + 
                        		"c. Mechanical Control\r\n" + 
                        		"Manually remove pests or block their entry.\r\n" + 
                        		"\r\n" + 
                        		"Traps: Use pheromone traps to capture rhinoceros beetles.\r\n" + 
                        		"Manual Removal: Handpick bagworms or caterpillars when infestations are small.\r\n" + 
                        		"Barriers: Install physical barriers around young palms to deter rodents.\r\n" + 
                        		"d. Chemical Control\r\n" + 
                        		"Apply pesticides when pest populations exceed economic thresholds.\r\n" + 
                        		"\r\n" + 
                        		"Selective Use: Choose pesticides that target specific pests to minimize harm to beneficial organisms.\r\n" + 
                        		"Application Timing: Apply during early mornings or evenings to protect non-target species like pollinators.\r\n" + 
                        		"Safety Measures: Follow guidelines to prevent chemical resistance and ensure worker safety.\r\n" + 
                        		"3. Monitoring and Early Detection\r\n" + 
                        		"Regular monitoring helps identify pest infestations early, reducing damage and control costs.\r\n" + 
                        		"\r\n" + 
                        		"Scouting: Conduct routine inspections of palms to detect signs of pests.\r\n" + 
                        		"Use of Technology: Leverage drones or sensors for large-scale monitoring.\r\n" + 
                        		"Threshold Levels: Define acceptable pest levels to determine when control measures are needed.\r\n" + 
                        		"4. Sustainable Practices in Pest Control\r\n" + 
                        		"Focus on eco-friendly approaches to balance pest control with environmental preservation.\r\n" + 
                        		"\r\n" + 
                        		"Minimize Pesticide Use: Opt for natural or organic alternatives whenever possible.\r\n" + 
                        		"Biodiversity: Encourage natural ecosystems by maintaining riparian zones and planting diverse vegetation.\r\n" + 
                        		"Training Workers: Educate plantation workers on sustainable pest management techniques.\r\n" + 
                        		"5. Record Keeping and Analysis\r\n" + 
                        		"Maintain records of pest infestations, control measures, and outcomes. This data helps refine strategies and improve long-term effectiveness.\r\n" + 
                        		"\r\n" + 
                        		"Conclusion\r\n" + 
                        		"Effective pest control is essential for healthy palm oil plantations and sustainable production. By combining biological, cultural, mechanical, and chemical methods under an Integrated Pest Management approach, plantation managers can minimize pest damage while preserving the environment and enhancing profitability.");
                        break;
                }
            }
        });

        HBox learningModuleBox = new HBox(20, moduleListView, articleDetails);
        content.getChildren().addAll(titleLabel, learningModuleBox);
        return content;
    }

    private VBox createPriceChartContent() {
        VBox content = new VBox(20);
        content.setPadding(new Insets(20));

        Label titleLabel = new Label("Price Chart");
        titleLabel.setStyle("-fx-font-size: 20px; -fx-font-weight: bold;");

        ComboBox<String> factoryComboBox = new ComboBox<>();
        factoryComboBox.setItems(FXCollections.observableArrayList("Factory A", "Factory B", "Factory C"));
        factoryComboBox.setValue(selectedFactory);

        currentPriceLabel = new Label(String.format("Current Price: Rp%.0f per kg", factoryPrices.get(selectedFactory)));
        currentPriceLabel.setStyle("-fx-font-size: 16px;");

        priceChart = createPriceChart(selectedFactory);

        factoryComboBox.setOnAction(e -> {
            selectedFactory = factoryComboBox.getValue();
            updatePriceChart(selectedFactory);
            currentPriceLabel.setText(String.format("Current Price: Rp%.0f per kg", factoryPrices.get(selectedFactory)));

            if (selectedFactoryLabel != null) {
                selectedFactoryLabel.setText("Selected Factory: " + selectedFactory);
                pricePerKgLabel.setText(String.format("Current Price: Rp%.0f per kg", factoryPrices.get(selectedFactory)));
            }
        });

        content.getChildren().addAll(titleLabel, factoryComboBox, currentPriceLabel, priceChart);
        return content;
    }

    private LineChart<Number, Number> createPriceChart(String factory) {
        NumberAxis xAxis = new NumberAxis();
        NumberAxis yAxis = new NumberAxis(2000, 5000, 500); // Set range to Rp2000 - Rp5000
        xAxis.setLabel("Days");
        yAxis.setLabel("Price per kg (Rp)");

        LineChart<Number, Number> lineChart = new LineChart<>(xAxis, yAxis);
        lineChart.setTitle("Price Trend");
        lineChart.setStyle("-fx-background-color: white;");
        lineChart.setPadding(new Insets(15));
        lineChart.setCreateSymbols(false);

        updateChartData(lineChart, factory);
        return lineChart;
    }

    private void updatePriceChart(String factory) {
        priceChart.getData().clear();
        updateChartData(priceChart, factory);
    }

    private void updateChartData(LineChart<Number, Number> chart, String factory) {
        XYChart.Series<Number, Number> series = new XYChart.Series<>();
        series.setName(factory);

        double basePrice = factoryPrices.get(factory);
        for (int i = 0; i < 30; i++) {
            double variation = (Math.random() - 0.5) * 1000;
            series.getData().add(new XYChart.Data<>(i, Math.max(2000, Math.min(5000, basePrice + variation))));
        }

        chart.getData().add(series);
    }

    private void showConfirmation(String title, String message) {
        Alert alert = new Alert(Alert.AlertType.INFORMATION);
        alert.setTitle(title);
        alert.setHeaderText(null);
        alert.setContentText(message);
        alert.showAndWait();
    }

    private void showError(String title, String message) {
        Alert alert = new Alert(Alert.AlertType.ERROR);
        alert.setTitle(title);
        alert.setHeaderText(null);
        alert.setContentText(message);
        alert.showAndWait();
    }

    public static class HarvestRecord {
        private final StringProperty date;
        private final StringProperty quantity;
        private final StringProperty factory;
        private final StringProperty status;

        public HarvestRecord(String date, String quantity, String factory, String status) {
            this.date = new SimpleStringProperty(date);
            this.quantity = new SimpleStringProperty(quantity);
            this.factory = new SimpleStringProperty(factory);
            this.status = new SimpleStringProperty(status);
        }

        public StringProperty dateProperty() {
            return date;
        }

        public StringProperty quantityProperty() {
            return quantity;
        }

        public StringProperty factoryProperty() {
            return factory;
        }

        public StringProperty statusProperty() {
            return status;
        }
    }

    public static class PaymentRecord {
        private final StringProperty date;
        private final StringProperty amount;
        private final StringProperty factory;
        private final StringProperty status;

        public PaymentRecord(String date, String amount, String factory, String status) {
            this.date = new SimpleStringProperty(date);
            this.amount = new SimpleStringProperty(amount);
            this.factory = new SimpleStringProperty(factory);
            this.status = new SimpleStringProperty(status);
        }

        public StringProperty dateProperty() {
            return date;
        }

        public StringProperty amountProperty() {
            return amount;
        }

        public StringProperty factoryProperty() {
            return factory;
        }

        public StringProperty statusProperty() {
            return status;
        }
    }

    public static void main(String[] args) {
        launch(args);
    }
}
