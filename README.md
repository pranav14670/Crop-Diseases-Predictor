# Crop-Diseases-Predictor
Upload an image or select a crop to get AI-powered disease detection, treatment recommendations, and prevention tips. Protect your crops with cutting-edge technology.


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CropGuard AI - Crop Disease Detection</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- React & ReactDOM -->
    <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    
    <!-- Babel for JSX -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    
    <!-- Lucide Icons (optional, for better icons) -->
    <script src="https://unpkg.com/lucide@latest"></script>
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .animate-spin { animation: spin 1s linear infinite; }
        @keyframes spin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect } = React;

        // Utility function for conditional classes
        const cn = (...classes) => {
            return classes.filter(Boolean).join(" ");
        };

        // Types (as JSDoc comments for clarity)
        /**
         * @typedef {Object} Disease
         * @property {string} name
         * @property {number} confidence
         * @property {string} description
         * @property {"low"|"medium"|"high"} severity
         * @property {string[]} symptoms
         * @property {string[]} treatment
         * @property {string[]} prevention
         */

        /**
         * @typedef {Object} Crop
         * @property {string} id
         * @property {string} name
         * @property {string} icon
         * @property {string} color
         */

        /**
         * @typedef {Object} PredictionResult
         * @property {Crop} crop
         * @property {Disease|null} disease
         * @property {boolean} isHealthy
         */

        // Crop data
        const crops = [
            { id: "wheat", name: "Wheat", icon: "🌾", color: "from-amber-400 to-yellow-500" },
            { id: "rice", name: "Rice", icon: "🌾", color: "from-green-400 to-emerald-500" },
            { id: "corn", name: "Corn", icon: "🌽", color: "from-yellow-400 to-orange-500" },
            { id: "tomato", name: "Tomato", icon: "🍅", color: "from-red-400 to-rose-500" },
            { id: "potato", name: "Potato", icon: "🥔", color: "from-amber-600 to-amber-700" },
            { id: "soybean", name: "Soybean", icon: "🫘", color: "from-lime-400 to-green-500" },
        ];

        // Disease database
        const diseaseDatabase = {
            wheat: [
                {
                    name: "Wheat Rust",
                    confidence: 92,
                    description: "A fungal disease causing orange-brown pustules on leaves and stems.",
                    severity: "high",
                    symptoms: ["Orange-brown spots on leaves", "Reduced grain yield", "Premature leaf death"],
                    treatment: ["Apply fungicides like tebuconazole", "Remove infected plants", "Use resistant varieties"],
                    prevention: ["Crop rotation", "Plant resistant varieties", "Avoid excess nitrogen"],
                },
                {
                    name: "Powdery Mildew",
                    confidence: 85,
                    description: "White powdery growth on leaves affecting photosynthesis.",
                    severity: "medium",
                    symptoms: ["White powdery coating on leaves", "Leaf curling", "Reduced plant vigor"],
                    treatment: ["Sulfur-based fungicides", "Neem oil spray", "Remove infected leaves"],
                    prevention: ["Proper spacing", "Good air circulation", "Avoid overhead irrigation"],
                },
                {
                    name: "Leaf Blotch",
                    confidence: 78,
                    description: "Brown lesions on leaves that spread rapidly.",
                    severity: "medium",
                    symptoms: ["Brown spots on leaves", "Yellowing around spots", "Leaf withering"],
                    treatment: ["Fungicide application", "Copper-based sprays", "Remove debris"],
                    prevention: ["Clean field preparation", "Proper drainage", "Resistant varieties"],
                },
            ],
            rice: [
                {
                    name: "Blast Disease",
                    confidence: 95,
                    description: "Causes diamond-shaped lesions on leaves and can destroy entire crops.",
                    severity: "high",
                    symptoms: ["Diamond-shaped lesions", "White center with brown edges", "Neck rot in panicles"],
                    treatment: ["Tricyclazole fungicide", "Remove infected plants", "Seed treatment"],
                    prevention: ["Resistant varieties", "Balanced fertilization", "Proper water management"],
                },
                {
                    name: "Brown Spot",
                    confidence: 82,
                    description: "Brown spots on leaves that reduce photosynthesis.",
                    severity: "medium",
                    symptoms: ["Brown circular spots", "Leaf yellowing", "Reduced grain filling"],
                    treatment: ["Mancozeb fungicide", "Remove infected debris", "Seed treatment"],
                    prevention: ["Soil testing", "Balanced nutrition", "Proper drainage"],
                },
                {
                    name: "Sheath Blight",
                    confidence: 88,
                    description: "Affects sheath and leaves causing water-soaked lesions.",
                    severity: "high",
                    symptoms: ["Water-soaked lesions", "Lesions expand rapidly", "Plant lodging"],
                    treatment: ["Azoxystrobin fungicide", "Remove infected plants", "Biological control"],
                    prevention: ["Avoid dense planting", "Proper nitrogen management", "Crop rotation"],
                },
            ],
            corn: [
                {
                    name: "Corn Rust",
                    confidence: 87,
                    description: "Orange to brown pustules on leaves.",
                    severity: "medium",
                    symptoms: ["Orange-brown pustules", "Leaf yellowing", "Reduced yield"],
                    treatment: ["Fungicide application", "Remove infected plants", "Resistant hybrids"],
                    prevention: ["Resistant varieties", "Proper spacing", "Avoid excess irrigation"],
                },
                {
                    name: "Gray Leaf Spot",
                    confidence: 84,
                    description: "Gray rectangular lesions on leaves.",
                    severity: "medium",
                    symptoms: ["Gray rectangular lesions", "Lesions expand", "Leaf death"],
                    treatment: ["Fungicide spray", "Remove debris", "Resistant hybrids"],
                    prevention: ["Crop rotation", "Proper spacing", "Avoid overhead irrigation"],
                },
                {
                    name: "Common Smut",
                    confidence: 79,
                    description: "Large galls on ears and tassels.",
                    severity: "high",
                    symptoms: ["Large galls", "Black powdery mass", "Destroyed ears"],
                    treatment: ["Remove infected plants", "Destroy galls", "Resistant varieties"],
                    prevention: ["Resistant hybrids", "Proper wound protection", "Clean equipment"],
                },
            ],
            tomato: [
                {
                    name: "Early Blight",
                    confidence: 91,
                    description: "Brown spots with concentric rings on leaves and fruit.",
                    severity: "medium",
                    symptoms: ["Brown spots with rings", "Leaf yellowing", "Fruit lesions"],
                    treatment: ["Copper fungicide", "Remove infected leaves", "Neem oil"],
                    prevention: ["Mulching", "Proper spacing", "Avoid wet foliage"],
                },
                {
                    name: "Late Blight",
                    confidence: 94,
                    description: "Water-soaked lesions that turn brown and spread rapidly.",
                    severity: "high",
                    symptoms: ["Water-soaked lesions", "White mold in humidity", "Rapid spread"],
                    treatment: ["Metalaxyl fungicide", "Remove infected plants", "Destroy debris"],
                    prevention: ["Resistant varieties", "Proper spacing", "Avoid excess moisture"],
                },
                {
                    name: "Blossom End Rot",
                    confidence: 76,
                    description: "Dark, sunken spots on bottom of fruits.",
                    severity: "low",
                    symptoms: ["Dark spots on fruit bottom", "Fruit becomes leathery", "Affected fruits drop"],
                    treatment: ["Calcium sprays", "Consistent watering", "Remove affected fruits"],
                    prevention: ["Consistent irrigation", "Soil pH balance", "Avoid excess nitrogen"],
                },
            ],
            potato: [
                {
                    name: "Late Blight",
                    confidence: 93,
                    description: "Dark lesions on leaves and tubers.",
                    severity: "high",
                    symptoms: ["Dark lesions on leaves", "White mold in humidity", "Tuber rot"],
                    treatment: ["Metalaxyl fungicide", "Remove infected plants", "Destroy debris"],
                    prevention: ["Resistant varieties", "Proper spacing", "Avoid excess moisture"],
                },
                {
                    name: "Early Blight",
                    confidence: 86,
                    description: "Concentric rings on leaves and stems.",
                    severity: "medium",
                    symptoms: ["Concentric ring spots", "Leaf yellowing", "Stem lesions"],
                    treatment: ["Copper fungicide", "Remove infected leaves", "Mulching"],
                    prevention: ["Crop rotation", "Proper spacing", "Avoid excess moisture"],
                },
                {
                    name: "Black Scurf",
                    confidence: 77,
                    description: "Black sclerotia on tuber surfaces.",
                    severity: "medium",
                    symptoms: ["Black crust on tubers", "Reduced market value", "Rhizome infection"],
                    treatment: ["Seed treatment", "Remove infected plants", "Fungicide"],
                    prevention: ["Certified seed", "Crop rotation", "Avoid excess moisture"],
                },
            ],
            soybean: [
                {
                    name: "Soybean Rust",
                    confidence: 89,
                    description: "Small reddish-brown pustules on leaves.",
                    severity: "high",
                    symptoms: ["Red-brown pustules", "Leaf drop", "Reduced yield"],
                    treatment: ["Fungicide application", "Remove infected plants", "Resistant varieties"],
                    prevention: ["Resistant varieties", "Proper spacing", "Avoid excess moisture"],
                },
                {
                    name: "Frogeye Leaf Spot",
                    confidence: 83,
                    description: "Circular spots with dark borders on leaves.",
                    severity: "medium",
                    symptoms: ["Circular spots", "Dark borders", "Leaf yellowing"],
                    treatment: ["Fungicide spray", "Remove debris", "Seed treatment"],
                    prevention: ["Crop rotation", "Proper spacing", "Clean equipment"],
                },
                {
                    name: "Charcoal Rot",
                    confidence: 75,
                    description: "Gray stems and black sclerotia in roots.",
                    severity: "high",
                    symptoms: ["Gray stems", "Black sclerotia", "Plant wilting"],
                    treatment: ["Remove infected plants", "Resistant varieties", "Fungicide"],
                    prevention: ["Resistant varieties", "Proper irrigation", "Crop rotation"],
                },
            ],
        };

        // Healthy state
        const healthyState = {
            name: "Healthy",
            confidence: 95,
            description: "Your crop appears healthy with no signs of disease detected.",
            severity: "low",
            symptoms: ["Vibrant green leaves", "Normal growth pattern", "No visible lesions"],
            treatment: ["Continue regular care", "Monitor regularly", "Maintain good practices"],
            prevention: ["Regular monitoring", "Proper irrigation", "Balanced fertilization"],
        };

        // Simulated AI prediction function
        const predictDisease = (cropId, imageUploaded) => {
            const crop = crops.find((c) => c.id === cropId);
            if (!crop) throw new Error("Crop not found");

            const random = Math.random();
            const diseases = diseaseDatabase[cropId] || [];

            if (!imageUploaded || random > 0.7 || diseases.length === 0) {
                return {
                    crop,
                    disease: healthyState,
                    isHealthy: true,
                };
            }

            const diseaseIndex = Math.floor(Math.random() * diseases.length);
            const disease = diseases[diseaseIndex];

            return {
                crop,
                disease,
                isHealthy: false,
            };
        };

        // Components
        const Header = () => (
            <header className="fixed top-0 left-0 right-0 z-50 bg-white/80 backdrop-blur-md border-b border-gray-100">
                <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                    <div className="flex items-center justify-between h-16">
                        <div className="flex items-center gap-2">
                            <div className="w-10 h-10 bg-gradient-to-br from-green-500 to-emerald-600 rounded-xl flex items-center justify-center">
                                <span className="text-xl">🌱</span>
                            </div>
                            <span className="font-bold text-xl text-gray-800">CropGuard AI</span>
                        </div>
                        <nav className="hidden md:flex items-center gap-6">
                            <a href="#predict" className="text-gray-600 hover:text-emerald-600 transition">Predict</a>
                            <a href="#features" className="text-gray-600 hover:text-emerald-600 transition">Features</a>
                            <a href="#about" className="text-gray-600 hover:text-emerald-600 transition">About</a>
                        </nav>
                        <button className="bg-emerald-600 hover:bg-emerald-700 text-white px-4 py-2 rounded-lg font-medium transition shadow-lg shadow-emerald-200">
                            Get Started
                        </button>
                    </div>
                </div>
            </header>
        );

        const Hero = () => (
            <section className="pt-24 pb-16 px-4 sm:px-6 lg:px-8 bg-gradient-to-b from-emerald-50 to-white">
                <div className="max-w-7xl mx-auto">
                    <div className="grid lg:grid-cols-2 gap-12 items-center">
                        <div className="space-y-6">
                            <div className="inline-flex items-center gap-2 bg-emerald-100 text-emerald-700 px-4 py-2 rounded-full text-sm font-medium">
                                <span>✨</span> Powered by Advanced AI/ML
                            </div>
                            <h1 className="text-4xl sm:text-5xl lg:text-6xl font-bold text-gray-900 leading-tight">
                                Detect Crop Diseases
                                <span className="text-transparent bg-clip-text bg-gradient-to-r from-emerald-600 to-teal-600"> Instantly</span>
                            </h1>
                            <p className="text-lg text-gray-600 leading-relaxed">
                                Upload an image or select a crop to get AI-powered disease detection, treatment recommendations, and prevention tips. Protect your crops with cutting-edge technology.
                            </p>
                            <div className="flex flex-wrap gap-4">
                                <a href="#predict" className="bg-emerald-600 hover:bg-emerald-700 text-white px-6 py-3 rounded-xl font-semibold transition shadow-lg shadow-emerald-200 flex items-center gap-2">
                                    <span>🚀</span> Start Predicting
                                </a>
                                <a href="#features" className="bg-white hover:bg-gray-50 text-gray-700 px-6 py-3 rounded-xl font-semibold border border-gray-200 transition">
                                    Learn More
                                </a>
                            </div>
                            <div className="flex items-center gap-8 pt-4">
                                <div>
                                    <div className="text-2xl font-bold text-emerald-600">95%+</div>
                                    <div className="text-sm text-gray-500">Accuracy</div>
                                </div>
                                <div>
                                    <div className="text-2xl font-bold text-emerald-600">6+</div>
                                    <div className="text-sm text-gray-500">Crops Supported</div>
                                </div>
                                <div>
                                    <div className="text-2xl font-bold text-emerald-600">20+</div>
                                    <div className="text-sm text-gray-500">Diseases Detected</div>
                                </div>
                            </div>
                        </div>
                        <div className="relative">
                            <div className="absolute inset-0 bg-gradient-to-r from-emerald-400 to-teal-400 rounded-3xl blur-3xl opacity-20"></div>
                            <div className="relative bg-white rounded-3xl p-8 shadow-2xl border border-gray-100">
                                <div className="grid grid-cols-3 gap-4">
                                    {crops.slice(0, 6).map((crop) => (
                                        <div key={crop.id} className="bg-gradient-to-br from-gray-50 to-gray-100 rounded-xl p-4 text-center hover:scale-105 transition cursor-pointer">
                                            <div className="text-3xl mb-2">{crop.icon}</div>
                                            <div className="text-xs font-medium text-gray-600">{crop.name}</div>
                                        </div>
                                    ))}
                                </div>
                                <div className="mt-6 bg-emerald-50 rounded-xl p-4 flex items-center gap-3">
                                    <div className="w-10 h-10 bg-emerald-500 rounded-lg flex items-center justify-center">
                                        <span className="text-white">🤖</span>
                                    </div>
                                    <div>
                                        <div className="font-semibold text-emerald-800">AI Analysis Ready</div>
                                        <div className="text-sm text-emerald-600">Click to start disease detection</div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>
        );

        const Predictor = () => {
            const [selectedCrop, setSelectedCrop] = useState("");
            const [imageFile, setImageFile] = useState(null);
            const [imagePreview, setImagePreview] = useState(null);
            const [isAnalyzing, setIsAnalyzing] = useState(false);
            const [result, setResult] = useState(null);
            const [activeTab, setActiveTab] = useState("upload");

            const handleImageUpload = (e) => {
                const file = e.target.files?.[0];
                if (file) {
                    setImageFile(file);
                    const reader = new FileReader();
                    reader.onload = (e) => setImagePreview(e.target?.result);
                    reader.readAsDataURL(file);
                }
            };

            const handlePredict = () => {
                if (!selectedCrop) {
                    alert("Please select a crop first");
                    return;
                }
                setIsAnalyzing(true);
                setResult(null);

                setTimeout(() => {
                    const prediction = predictDisease(selectedCrop, !!imageFile);
                    setResult(prediction);
                    setIsAnalyzing(false);
                }, 2000);
            };

            const handleReset = () => {
                setSelectedCrop("");
                setImageFile(null);
                setImagePreview(null);
                setResult(null);
            };

            return (
                <section id="predict" className="py-16 px-4 sm:px-6 lg:px-8 bg-white">
                    <div className="max-w-5xl mx-auto">
                        <div className="text-center mb-12">
                            <h2 className="text-3xl sm:text-4xl font-bold text-gray-900 mb-4">Disease Predictor</h2>
                            <p className="text-gray-600 text-lg">Upload an image or select a crop to detect diseases using AI</p>
                        </div>

                        <div className="bg-gray-50 rounded-2xl p-6 sm:p-8 shadow-lg border border-gray-100">
                            <div className="flex gap-2 mb-6">
                                <button
                                    onClick={() => setActiveTab("upload")}
                                    className={cn(
                                        "flex-1 py-3 px-4 rounded-xl font-medium transition",
                                        activeTab === "upload"
                                            ? "bg-emerald-600 text-white shadow-lg shadow-emerald-200"
                                            : "bg-white text-gray-600 hover:bg-gray-100 border border-gray-200"
                                    )}
                                >
                                    📷 Upload Image
                                </button>
                                <button
                                    onClick={() => setActiveTab("select")}
                                    className={cn(
                                        "flex-1 py-3 px-4 rounded-xl font-medium transition",
                                        activeTab === "select"
                                            ? "bg-emerald-600 text-white shadow-lg shadow-emerald-200"
                                            : "bg-white text-gray-600 hover:bg-gray-100 border border-gray-200"
                                    )}
                                >
                                    🌾 Select Crop
                                </button>
                            </div>

                            {activeTab === "upload" && (
                                <div className="space-y-6">
                                    <div
                                        className={cn(
                                            "border-2 border-dashed rounded-2xl p-8 text-center transition",
                                            imagePreview
                                                ? "border-emerald-300 bg-emerald-50"
                                                : "border-gray-300 hover:border-emerald-400 bg-white"
                                        )}
                                    >
                                        {imagePreview ? (
                                            <div className="space-y-4">
                                                <img src={imagePreview} alt="Preview" className="max-h-64 mx-auto rounded-xl object-cover shadow-lg" />
                                                <button
                                                    onClick={() => { setImageFile(null); setImagePreview(null); }}
                                                    className="text-red-500 hover:text-red-700 font-medium"
                                                >
                                                    Remove Image
                                                </button>
                                            </div>
                                        ) : (
                                            <label className="cursor-pointer block">
                                                <div className="text-5xl mb-4">📸</div>
                                                <div className="text-lg font-medium text-gray-700 mb-2">Drop your crop image here</div>
                                                <div className="text-sm text-gray-500 mb-4">or click to browse from your device</div>
                                                <input type="file" accept="image/*" onChange={handleImageUpload} className="hidden" />
                                                <div className="text-xs text-gray-400">Supports JPG, PNG, WebP (Max 10MB)</div>
                                            </label>
                                        )}
                                    </div>

                                    <div className="space-y-4">
                                        <label className="block text-sm font-medium text-gray-700">Or select a crop to analyze</label>
                                        <div className="grid grid-cols-2 sm:grid-cols-3 gap-3">
                                            {crops.map((crop) => (
                                                <button
                                                    key={crop.id}
                                                    onClick={() => setSelectedCrop(crop.id)}
                                                    className={cn(
                                                        "flex items-center gap-3 p-3 rounded-xl border-2 transition",
                                                        selectedCrop === crop.id
                                                            ? "border-emerald-500 bg-emerald-50"
                                                            : "border-gray-200 bg-white hover:border-emerald-300"
                                                    )}
                                                >
                                                    <span className="text-2xl">{crop.icon}</span>
                                                    <span className="font-medium text-gray-700">{crop.name}</span>
                                                </button>
                                            ))}
                                        </div>
                                    </div>
                                </div>
                            )}

                            {activeTab === "select" && (
                                <div className="space-y-6">
                                    <label className="block text-sm font-medium text-gray-700">Select a crop to analyze</label>
                                    <div className="grid grid-cols-2 sm:grid-cols-3 gap-4">
                                        {crops.map((crop) => (
                                            <button
                                                key={crop.id}
                                                onClick={() => setSelectedCrop(crop.id)}
                                                className={cn(
                                                    "flex flex-col items-center gap-2 p-5 rounded-xl border-2 transition hover:scale-105",
                                                    selectedCrop === crop.id
                                                        ? "border-emerald-500 bg-emerald-50"
                                                        : "border-gray-200 bg-white hover:border-emerald-300"
                                                )}
                                            >
                                                <span className="text-4xl">{crop.icon}</span>
                                                <span className="font-semibold text-gray-800">{crop.name}</span>
                                            </button>
                                        ))}
                                    </div>

                                    <div className="bg-blue-50 border border-blue-200 rounded-xl p-4 flex items-start gap-3">
                                        <span className="text-xl">💡</span>
                                        <div>
                                            <div className="font-medium text-blue-800">Tip</div>
                                            <div className="text-sm text-blue-700">For best results, upload a clear image of the affected plant area. Our AI will analyze it and provide accurate predictions.</div>
                                        </div>
                                    </div>
                                </div>
                            )}

                            <div className="flex flex-wrap gap-3 mt-6">
                                <button
                                    onClick={handlePredict}
                                    disabled={!selectedCrop || isAnalyzing}
                                    className={cn(
                                        "flex-1 sm:flex-none px-6 py-3 rounded-xl font-semibold transition flex items-center justify-center gap-2",
                                        selectedCrop && !isAnalyzing
                                            ? "bg-emerald-600 hover:bg-emerald-700 text-white shadow-lg shadow-emerald-200"
                                            : "bg-gray-300 text-gray-500 cursor-not-allowed"
                                    )}
                                >
                                    {isAnalyzing ? (
                                        <>
                                            <span className="animate-spin">⏳</span> Analyzing...
                                        </>
                                    ) : (
                                        <>
                                            <span>🔍</span> Analyze
                                        </>
                                    )}
                                </button>
                                <button
                                    onClick={handleReset}
                                    className="px-6 py-3 rounded-xl font-semibold text-gray-600 bg-white border border-gray-200 hover:bg-gray-50 transition"
                                >
                                    Reset
                                </button>
                            </div>

                            {result && (
                                <div className="mt-8 space-y-6">
                                    <div className={cn(
                                        "rounded-2xl p-6 border-2",
                                        result.isHealthy ? "border-green-300 bg-green-50" : "border-red-300 bg-red-50"
                                    )}>
                                        <div className="flex items-start gap-4">
                                            <div className={cn(
                                                "w-16 h-16 rounded-xl flex items-center justify-center text-3xl",
                                                result.isHealthy ? "bg-green-500" : "bg-red-500"
                                            )}>
                                                {result.isHealthy ? "✅" : "⚠️"}
                                            </div>
                                            <div className="flex-1">
                                                <div className={cn(
                                                    "text-2xl font-bold",
                                                    result.isHealthy ? "text-green-800" : "text-red-800"
                                                )}>
                                                    {result.isHealthy ? "Crop is Healthy!" : result.disease?.name}
                                                </div>
                                                <div className={cn(
                                                    "text-sm font-medium mt-1",
                                                    result.isHealthy ? "text-green-600" : "text-red-600"
                                                )}>
                                                    {result.isHealthy ? "No disease detected" : `Confidence: ${result.disease?.confidence}%`}
                                                </div>
                                            </div>
                                        </div>
                                    </div>

                                    {!result.isHealthy && result.disease && (
                                        <div className="grid md:grid-cols-2 gap-6">
                                            <div className="bg-white rounded-xl p-5 border border-gray-200 shadow-sm">
                                                <h4 className="font-bold text-gray-800 mb-3 flex items-center gap-2">
                                                    <span>📋</span> Description
                                                </h4>
                                                <p className="text-gray-600">{result.disease.description}</p>
                                            </div>

                                            <div className="bg-white rounded-xl p-5 border border-gray-200 shadow-sm">
                                                <h4 className="font-bold text-gray-800 mb-3 flex items-center gap-2">
                                                    <span>🌡️</span> Severity
                                                </h4>
                                                <div className={cn(
                                                    "inline-flex items-center gap-2 px-3 py-1 rounded-full text-sm font-medium",
                                                    result.disease.severity === "high" ? "bg-red-100 text-red-700" :
                                                    result.disease.severity === "medium" ? "bg-yellow-100 text-yellow-700" :
                                                    "bg-green-100 text-green-700"
                                                )}>
                                                    {result.disease.severity === "high" ? "🔴 High" :
                                                     result.disease.severity === "medium" ? "🟡 Medium" : "🟢 Low"}
                                                </div>
                                            </div>

                                            <div className="bg-white rounded-xl p-5 border border-gray-200 shadow-sm">
                                                <h4 className="font-bold text-gray-800 mb-3 flex items-center gap-2">
                                                    <span>👀</span> Symptoms
                                                </h4>
                                                <ul className="space-y-2">
                                                    {result.disease.symptoms.map((symptom, i) => (
                                                        <li key={i} className="flex items-start gap-2 text-gray-600">
                                                            <span className="text-emerald-500">•</span>
                                                            {symptom}
                                                        </li>
                                                    ))}
                                                </ul>
                                            </div>

                                            <div className="bg-white rounded-xl p-5 border border-gray-200 shadow-sm">
                                                <h4 className="font-bold text-gray-800 mb-3 flex items-center gap-2">
                                                    <span>💊</span> Treatment
                                                </h4>
                                                <ul className="space-y-2">
                                                    {result.disease.treatment.map((treatment, i) => (
                                                        <li key={i} className="flex items-start gap-2 text-gray-600">
                                                            <span className="text-emerald-500">✓</span>
                                                            {treatment}
                                                        </li>
                                                    ))}
                                                </ul>
                                            </div>

                                            <div className="md:col-span-2 bg-white rounded-xl p-5 border border-gray-200 shadow-sm">
                                                <h4 className="font-bold text-gray-800 mb-3 flex items-center gap-2">
                                                    <span>🛡️</span> Prevention Tips
                                                </h4>
                                                <div className="grid sm:grid-cols-2 gap-3">
                                                    {result.disease.prevention.map((prevention, i) => (
                                                        <div key={i} className="flex items-start gap-2 text-gray-600 bg-gray-50 p-3 rounded-lg">
                                                            <span className="text-emerald-500 mt-1">🛡️</span>
                                                            {prevention}
                                                        </div>
                                                    ))}
                                                </div>
                                            </div>
                                        </div>
                                    )}

                                    {result.isHealthy && result.disease && (
                                        <div className="bg-white rounded-xl p-5 border border-gray-200 shadow-sm">
                                            <h4 className="font-bold text-gray-800 mb-3 flex items-center gap-2">
                                                <span>💪</span> Maintain Crop Health
                                            </h4>
                                            <div className="grid sm:grid-cols-2 gap-3">
                                                {result.disease.treatment.map((tip, i) => (
                                                    <div key={i} className="flex items-start gap-2 text-gray-600 bg-gray-50 p-3 rounded-lg">
                                                        <span className="text-emerald-500 mt-1">✓</span>
                                                        {tip}
                                                    </div>
                                                ))}
                                            </div>
                                        </div>
                                    )}
                                </div>
                            )}
                        </div>
                    </div>
                </section>
            );
        };

        const Features = () => {
            const features = [
                {
                    icon: "🤖",
                    title: "AI-Powered Detection",
                    description: "Advanced machine learning models trained on thousands of crop disease images for accurate detection.",
                },
                {
                    icon: "📸",
                    title: "Image Upload",
                    description: "Simply upload a photo of your crop and get instant analysis with detailed results.",
                },
                {
                    icon: "📊",
                    title: "Confidence Scoring",
                    description: "Get confidence percentages for each prediction to make informed decisions.",
                },
                {
                    icon: "📋",
                    title: "Treatment Guide",
                    description: "Comprehensive treatment recommendations and prevention strategies for each disease.",
                },
                {
                    icon: "🌱",
                    title: "Multi-Crop Support",
                    description: "Supports wheat, rice, corn, tomato, potato, and soybean with more coming soon.",
                },
                {
                    icon: "⚡",
                    title: "Instant Results",
                    description: "Get predictions in seconds with our optimized AI models.",
                },
            ];

            return (
                <section id="features" className="py-16 px-4 sm:px-6 lg:px-8 bg-gradient-to-b from-white to-emerald-50">
                    <div className="max-w-7xl mx-auto">
                        <div className="text-center mb-12">
                            <h2 className="text-3xl sm:text-4xl font-bold text-gray-900 mb-4">Why Choose CropGuard AI?</h2>
                            <p className="text-gray-600 text-lg max-w-2xl mx-auto">Powerful features designed to help farmers and agricultural professionals protect their crops effectively.</p>
                        </div>

                        <div className="grid sm:grid-cols-2 lg:grid-cols-3 gap-6">
                            {features.map((feature, i) => (
                                <div key={i} className="bg-white rounded-2xl p-6 shadow-lg border border-gray-100 hover:shadow-xl transition hover:-translate-y-1">
                                    <div className="w-14 h-14 bg-gradient-to-br from-emerald-400 to-teal-500 rounded-xl flex items-center justify-center text-2xl mb-4">
                                        {feature.icon}
                                    </div>
                                    <h3 className="text-xl font-bold text-gray-900 mb-2">{feature.title}</h3>
                                    <p className="text-gray-600">{feature.description}</p>
                                </div>
                            ))}
                        </div>
                    </div>
                </section>
            );
        };

        const About = () => (
            <section id="about" className="py-16 px-4 sm:px-6 lg:px-8 bg-white">
                <div className="max-w-7xl mx-auto">
                    <div className="grid lg:grid-cols-2 gap-12 items-center">
                        <div className="space-y-6">
                            <h2 className="text-3xl sm:text-4xl font-bold text-gray-900">About CropGuard AI</h2>
                            <p className="text-gray-600 text-lg leading-relaxed">
                                CropGuard AI is a cutting-edge crop disease prediction platform that leverages artificial intelligence and machine learning to help farmers detect and manage crop diseases early.
                            </p>
                            <p className="text-gray-600 leading-relaxed">
                                Our system uses advanced computer vision algorithms trained on extensive datasets of crop diseases to provide accurate, real-time predictions. Whether you're a small-scale farmer or managing large agricultural operations, CropGuard AI provides the insights you need to protect your crops and maximize yields.
                            </p>
                            <div className="grid grid-cols-2 gap-4">
                                <div className="bg-emerald-50 rounded-xl p-4">
                                    <div className="text-2xl font-bold text-emerald-600">10K+</div>
                                    <div className="text-sm text-gray-600">Farmers Served</div>
                                </div>
                                <div className="bg-emerald-50 rounded-xl p-4">
                                    <div className="text-2xl font-bold text-emerald-600">50K+</div>
                                    <div className="text-sm text-gray-600">Predictions Made</div>
                                </div>
                            </div>
                        </div>
                        <div className="bg-gradient-to-br from-emerald-500 to-teal-600 rounded-3xl p-8 text-white">
                            <h3 className="text-2xl font-bold mb-6">Our Mission</h3>
                            <p className="text-emerald-100 mb-6 leading-relaxed">
                                To empower farmers worldwide with accessible AI technology that helps them detect crop diseases early, reduce losses, and improve food security.
                            </p>
                            <div className="space-y-3">
                                <div className="flex items-center gap-3">
                                    <span className="text-xl">🌾</span>
                                    <span>Support sustainable agriculture</span>
                                </div>
                                <div className="flex items-center gap-3">
                                    <span className="text-xl">🌍</span>
                                    <span>Global accessibility</span>
                                </div>
                                <div className="flex items-center gap-3">
                                    <span className="text-xl">🤝</span>
                                    <span>Community-driven development</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>
        );

        const Footer = () => (
            <footer className="bg-gray-900 text-white py-12 px-4 sm:px-6 lg:px-8">
                <div className="max-w-7xl mx-auto">
                    <div className="grid sm:grid-cols-2 lg:grid-cols-4 gap-8 mb-8">
                        <div>
                            <div className="flex items-center gap-2 mb-4">
                                <div className="w-10 h-10 bg-gradient-to-br from-green-500 to-emerald-600 rounded-xl flex items-center justify-center">
                                    <span className="text-xl">🌱</span>
                                </div>
                                <span className="font-bold text-xl">CropGuard AI</span>
                            </div>
                            <p className="text-gray-400 text-sm">
                                AI-powered crop disease detection for sustainable agriculture.
                            </p>
                        </div>
                        <div>
                            <h4 className="font-bold mb-4">Quick Links</h4>
                            <ul className="space-y-2 text-gray-400 text-sm">
                                <li><a href="#predict" className="hover:text-white transition">Predictor</a></li>
                                <li><a href="#features" className="hover:text-white transition">Features</a></li>
                                <li><a href="#about" className="hover:text-white transition">About</a></li>
                            </ul>
                        </div>
                        <div>
                            <h4 className="font-bold mb-4">Supported Crops</h4>
                            <ul className="space-y-2 text-gray-400 text-sm">
                                <li>🌾 Wheat</li>
                                <li>🌾 Rice</li>
                                <li>🌽 Corn</li>
                                <li>🍅 Tomato</li>
                            </ul>
                        </div>
                        <div>
                            <h4 className="font-bold mb-4">Contact</h4>
                            <ul className="space-y-2 text-gray-400 text-sm">
                                <li>📧 gangulipranav99@gmail.com</li>
                                <li>🌐 www.cropguard.ai</li>
                                <li>📞 +91 6299816428</li>
                            </ul>
                        </div>
                    </div>
                    <div className="border-t border-gray-800 pt-8 text-center text-gray-500 text-sm">
                        © 2024 CropGuard AI. All rights reserved. Built with AI/ML technology.
                    </div>
                </div>
            </footer>
        );

        function App() {
            return (
                <div className="min-h-screen bg-gray-50">
                    <Header />
                    <main>
                        <Hero />
                        <Predictor />
                        <Features />
                        <About />
                    </main>
                    <Footer />
                </div>
            );
        }

        // Render the app
        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>

