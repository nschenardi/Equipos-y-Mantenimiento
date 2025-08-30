document.addEventListener('DOMContentLoaded', () => {
    // --- State Management ---
    const state = {
        history: ['login-screen'],
        capturedPhotos: []
    };

    // --- DOM Elements ---
    const loginBtn = document.getElementById('login-btn');
    const navButtons = document.querySelectorAll('.nav-btn');
    const backButtons = document.querySelectorAll('.back-btn');
    const saveButtons = document.querySelectorAll('.save-btn');
    const cameraButtons = document.querySelectorAll('.camera-btn');
    const captureBtn = document.getElementById('capture-btn');
    const savePhotosBtn = document.getElementById('save-photos-btn');
    const photoCountSpan = document.getElementById('photo-count');
    const video = document.getElementById('video-feed');
    const canvas = document.getElementById('canvas');

    // --- Form Definitions ---
    const formsData = {
        'turboexpansor-form': ['Presión Entrada Expansor (Exp. In P) (PIC 301B)', 'Temperatura Entrada Expansor (Exp. In T)(TI0314)', 'Presión Salida Expansor (Exp. Out P) (PIC 306)', 'Temperatura Salida Expansor (Exp. Out T)(TI0326)', 'Presión Rueda Expansor (Exp. Wheel P) PI-4', 'Presión Entrada Compresor (Comp. In P) PI-309 (PIC0302)', 'Temperatura Entrada Compresor (Comp. In T) TI0301', 'Presión Salida Compresor (Comp. Out P) PI-307', 'Temperatura Salida Compresor (Comp Out T)', 'Presión Rueda Compresor (Comp. Wheel P) PI-8', 'Presión Empuje Expansor (Drive Bearing Thrust PI-6)', 'Presión Empuje Compresor (Load Bearing Thurst PI-7)', 'Presión Reservorio (Reservoir P) PI-2', 'Temperatura Reservorio (Reservoir T)', 'Presión Aceite Lubricante (Lube Oil P) PI-3', 'Diferencia de Presión Aceite Lubricante (Lube Oil DP)', 'Presión Gas de Sello (Seal Gas P) PI-5', 'Diferencia de Presión Gas de Sello (Seal Gas DP)', 'RPM', 'Temperatura Cojinetes Expansor (Exp. Brg. T)', 'Temperatura Cojinetes Compresor (Comp. Brg. T)', 'Vibración X Expansor (Exp. Vib. X)', 'Vibración X Compresor (Comp. Vib. X)', 'Temperatura Entrada Aceite Lubricante (Lube Oil In T)', 'Temperatura Aceite Drenaje (Oil Drain T)', 'Temperatura Entrada Gas de Sello (Seal Gas In T)', '% Aletas Guías (PIC0301B)', 'JT % (PIC0301A)', 'Reciclo FIC0301 (Recycle %) Porcentaje apertura', 'FIC0301 Valor de caudal (porcentaje)', 'Flujo Expansor (FIC0101)', 'Flujo Compresor (FI0102+FIC0201)', 'T° Ambiente (TI0100) (°F)', 'Presión Suministro Gas de Sello', 'T° Suministro Gas de Sello', 'Caudal Gas de Sello FI-1', 'NIVEL ACEITE RESERVORIO'],
        'recompresores-form': ['Caudal de Succión', 'T. Ambiente', 'R.P.M.', 'Motor', 'Pres de Dif Filtros Aceite Motor', 'Pres de Aceite Motor Tablero', 'Pres de Aceite Compresor Tablero', 'Pres de Aceite Compresor Entrada', 'Pres de Aceite Compresor Salida', 'Pres Desc Comp Cilindro 1', 'Pres Desc Comp Cilindro 2', 'Temp Descarga Cilindro Izquierdo', 'Temp Descarga Cilindro Derecho', 'Pres Succión Compresor', 'Temp Admisión Motor Banco Izq.', 'Temp Admisión Motor Banco Der.', 'Temperatura Aceite Motor', 'Temperatura Aceite Compresor', 'Temperatura Agua Principal', 'Presión Múltiple Admisión Izquierdo', 'Presión Múltiple Admisión Derecho', 'Filtros de aire Derecho', 'Filtros de aire Izquierdo'],
        'motocompresores-form': ['PI5107 Presión de succión DCS', 'FI5107 Caudal gas en baja presión', 'Presión de Succión', 'Presión descarga 1° etapa', 'Presión descarga 2° etapa', 'Presión descarga 3° etapa', 'Presión Aceite Motor', 'Presión aceite Compresor', 'Presión refrigerante Sist. Principal', 'Presión refrigerante Sist. Aux', 'Presión manifold de aire Bco.Izq', 'Presión Carter', 'Presión manifold de aire Bco.Der', 'Vibración Motor Lado Acople', 'Vibración Motor Lado Libre', 'Vibración Compresor Lado Acople', 'Vibración Compresor Lado Libre', 'Vibración Cooler', 'Temperatura Refrigerante Sist. Principal', 'Temperatura Refrigerante Sist. Auxiliar', 'Temperatura Aceite Compresor', 'Temperatura Aceite Motor', 'Temperatura desc. Compresor Throw #1', 'Temperatura desc. Compresor Throw #2', 'Temperatura desc. Compresor Throw #3', 'Temperatura desc. Compresor Throw #4', 'Temperatura Cojinete Bancada Motor #1', 'Temperatura Cojinete Bancada Motor #2', 'Temperatura Cojinete Bancada Motor #3', 'Temperatura Cojinete Bancada Motor #4', 'Temperatura Cojinete Bancada Motor #5', 'Temperatura Cojinete Bancada Motor #6', 'Temperatura Cojinete Bancada Motor #7', 'Temperatura entrada Turbo Bco. Izq.', 'Temperatura entrada Turbo Bco. Der.', 'Temperatura Succión', 'Temperatura Ambiente', 'Temperatura Escape cilindro #1', 'Temperatura Escape cilindro #2', 'Temperatura Escape cilindro #3', 'Temperatura Escape cilindro #4', 'Temperatura Escape cilindro #5', 'Temperatura Escape cilindro #6', 'Temperatura Escape cilindro #7', 'Temperatura Escape cilindro #8', 'Temperatura Escape cilindro #9', 'Temperatura Escape cilindro #10', 'Temperatura Escape cilindro #11', 'Temperatura Escape cilindro #12', 'Presión diferencial Filtros compresor (PSI)', 'Presión diferencial Filtros motor (PSI)', 'Porcentaje de carga (max 85%) ESM', 'Nivel de aceite motor', 'Nivel aceite compresor', 'Nivel refrigerante Principal', 'Nivel refrigerante Auxiliar'],
        'frick-form': ['Presión de succión', 'Temperatura de descarga', 'Presión de descarga'],
        'lpg-compressors-form': ['Presión de succión', 'Temperatura de descarga', 'Presión de descarga'],
        'lpg-tanks-form': ['Presión de succión', 'Temperatura de descarga', 'Presión de descarga']
    };

    // --- Functions ---
    const showScreen = (screenId) => {
        document.querySelectorAll('.screen').forEach(screen => screen.classList.remove('active'));
        const targetScreen = document.getElementById(screenId);
        if (targetScreen) {
            targetScreen.classList.add('active');
            // Restore data for form screens
            if (targetScreen.classList.contains('form-screen')) {
                restoreFormData(targetScreen.querySelector('form').id);
            }
        }
    };

    const navigateTo = (screenId) => {
        showScreen(screenId);
        state.history.push(screenId);
    };

    const goBack = () => {
        if (state.history.length > 1) {
            state.history.pop();
            const lastScreen = state.history[state.history.length - 1];
            showScreen(lastScreen);
             // Stop camera if navigating away from it
            if (video.srcObject) {
                video.srcObject.getTracks().forEach(track => track.stop());
                video.srcObject = null;
            }
        }
    };

    const updateDateTime = () => {
        const now = new Date();
        const options = { year: 'numeric', month: 'numeric', day: 'numeric', hour: '2-digit', minute: '2-digit', second: '2-digit' };
        document.getElementById('datetime').textContent = now.toLocaleDateString('es-ES', options);
    };

    const handleLogin = () => {
        const user = document.getElementById('username').value;
        const pass = document.getElementById('password').value;
        if (user === 'PTG2025' && pass === 'Clave2025') {
            navigateTo('main-menu-screen');
            updateDateTime();
            setInterval(updateDateTime, 1000);
        } else {
            alert('Usuario o contraseña incorrectos.');
        }
    };
    
    const generateForms = () => {
        for (const formId in formsData) {
            const formElement = document.getElementById(formId);
            if(formElement) {
                const fields = formsData[formId];
                let html = '';
                fields.forEach((field, index) => {
                    const inputId = `${formId}-field-${index}`;
                    html += `
                        <div class="form-group">
                            <label for="${inputId}">${field}</label>
                            <input type="text" id="${inputId}" name="${field}" class="form-input">
                        </div>
                    `;
                });
                formElement.innerHTML = html;
            }
        }
    };

    const saveFormData = (e) => {
        if(e.target.classList.contains('form-input')) {
            sessionStorage.setItem(e.target.id, e.target.value);
        }
    };
    
    const restoreFormData = (formId) => {
        const form = document.getElementById(formId);
        if(!form) return;
        const inputs = form.querySelectorAll('.form-input');
        inputs.forEach(input => {
            const savedValue = sessionStorage.getItem(input.id);
            if (savedValue) {
                input.value = savedValue;
            }
        });
    };

    const handleEnterKey = (e) => {
        if (e.key === 'Enter' && e.target.classList.contains('form-input')) {
            e.preventDefault();
            const form = e.target.closest('form');
            const inputs = Array.from(form.querySelectorAll('.form-input'));
            const currentIndex = inputs.indexOf(e.target);
            const nextInput = inputs[currentIndex + 1];
            if (nextInput) {
                nextInput.focus();
            } else {
                 // Optionally, blur the last input or focus the save button
                 e.target.blur();
            }
        }
    };

    const exportToXLS = (e) => {
        const formId = e.target.dataset.form;
        const filename = e.target.dataset.filename;
        const form = document.getElementById(formId);
        if (!form) return;

        let csvContent = "data:text/csv;charset=utf-8,";
        csvContent += "Campo,Valor\r\n"; // Header Row

        const inputs = form.querySelectorAll('.form-input');
        inputs.forEach(input => {
            const label = document.querySelector(`label[for='${input.id}']`).textContent;
            csvContent += `"${label.replace(/"/g, '""')}","${input.value.replace(/"/g, '""')}"\r\n`;
        });
        
        const encodedUri = encodeURI(csvContent);
        const link = document.createElement("a");
        link.setAttribute("href", encodedUri);
        link.setAttribute("download", filename);
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    };

    // --- Camera Logic ---
    const startCamera = async () => {
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ video: true });
            video.srcObject = stream;
            state.capturedPhotos = []; // Reset photos when starting camera
            updatePhotoCount();
        } catch (err) {
            console.error("Error accessing camera: ", err);
            alert("No se pudo acceder a la cámara. Asegúrese de tener una y de haber otorgado los permisos.");
            goBack();
        }
    };

    const handleCapture = () => {
        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        const context = canvas.getContext('2d');
        context.drawImage(video, 0, 0, canvas.width, canvas.height);
        const dataUrl = canvas.toDataURL('image/jpeg');
        state.capturedPhotos.push(dataUrl);
        updatePhotoCount();
    };

    const updatePhotoCount = () => {
        photoCountSpan.textContent = state.capturedPhotos.length;
    };
    
    const savePhotos = () => {
        if (state.capturedPhotos.length === 0) {
            alert("No hay fotos para guardar.");
            return;
        }
        state.capturedPhotos.forEach((photoDataUrl, index) => {
            const link = document.createElement('a');
            link.href = photoDataUrl;
            const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
            link.download = `captura-${timestamp}-${index + 1}.jpg`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        });
        alert(`${state.capturedPhotos.length} foto(s) guardada(s) en la carpeta de descargas.`);
    };


    // --- Event Listeners ---
    loginBtn.addEventListener('click', handleLogin);
    navButtons.forEach(btn => btn.addEventListener('click', (e) => navigateTo(e.target.dataset.target)));
    backButtons.forEach(btn => btn.addEventListener('click', goBack));
    saveButtons.forEach(btn => btn.addEventListener('click', exportToXLS));
    cameraButtons.forEach(btn => btn.addEventListener('click', startCamera));
    captureBtn.addEventListener('click', handleCapture);
    savePhotosBtn.addEventListener('click', savePhotos);

    // Add listeners for data persistence and enter key
    document.addEventListener('keyup', saveFormData);
    document.addEventListener('keydown', handleEnterKey);


    // --- Initial Setup ---
    generateForms();
    showScreen(state.history[0]);
});
