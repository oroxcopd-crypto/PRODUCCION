<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PROD CONTROL - Control de Producción con Scrap</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Versión actualizada de jsPDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        :root {
            --primary: #3b82f6;
            --primary-dark: #1d4ed8;
            --secondary: #06b6d4;
            --success: #10b981;
            --warning: #f59e0b;
            --danger: #ef4444;
            --dark: #1f2937;
            --light: #f8fafc;
            --gray: #6b7280;
            --border: #e5e7eb;
            --card-shadow: 0 1px 2px rgba(0,0,0,0.05);
            --transition: all 0.15s ease;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', system-ui, sans-serif;
        }

        body {
            background: #f1f5f9;
            color: var(--dark);
            min-height: 100vh;
            padding: 10px;
        }

        /* PANTALLA DE LOGIN - OPTIMIZADA PARA MÓVIL */
        .login-container {
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            background: linear-gradient(135deg, #0D47A1 0%, #1976D2 100%);
            z-index: 2000;
            transition: opacity 0.5s ease;
        }

        .login-box {
            background: white;
            padding: 30px 25px;
            border-radius: 16px;
            box-shadow: 0 20px 50px rgba(0, 0, 0, 0.3);
            width: 90%;
            max-width: 400px;
            text-align: center;
            animation: slideUp 0.6s ease;
        }

        .login-logo {
            font-size: 3.5rem;
            color: var(--primary);
            margin-bottom: 20px;
            animation: pulse 2s infinite;
        }

        .login-box h1 {
            color: var(--primary);
            margin-bottom: 10px;
            font-size: 1.8rem;
            font-weight: 700;
        }

        .login-box p {
            color: var(--text-light);
            margin-bottom: 25px;
            font-size: 1rem;
            line-height: 1.4;
        }

        .login-input {
            width: 100%;
            padding: 16px;
            margin-bottom: 20px;
            border: 2px solid var(--border);
            border-radius: 12px;
            font-size: 1.2rem;
            text-align: center;
            transition: all 0.3s ease;
            letter-spacing: 8px;
            font-weight: 600;
            background: #f8fafc;
            inputmode: numeric;
            maxlength: 4;
        }

        .login-input:focus {
            border-color: var(--primary);
            box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
            outline: none;
        }

        .login-btn {
            width: 100%;
            padding: 18px;
            background: var(--primary);
            color: white;
            border: none;
            border-radius: 12px;
            font-size: 1.1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
        }

        .login-btn:hover {
            background: var(--primary-dark);
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0,0,0,0.15);
        }

        .login-error {
            color: #f44336;
            margin-top: 10px;
            font-size: 0.9rem;
            display: none;
            padding: 8px;
            background: #fee;
            border-radius: 6px;
            border-left: 4px solid #f44336;
        }

        .password-hint {
            color: #666;
            font-size: 0.85rem;
            margin-top: 20px;
            text-align: center;
            background: #f9f9f9;
            padding: 12px;
            border-radius: 8px;
            border: 1px solid #e5e7eb;
        }

        .keypad {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin-top: 20px;
        }

        .keypad-btn {
            padding: 20px;
            background: white;
            border: 2px solid #e5e7eb;
            border-radius: 12px;
            font-size: 1.5rem;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.2s ease;
            touch-action: manipulation;
        }

        .keypad-btn:active {
            background: #f1f5f9;
            transform: scale(0.95);
        }

        .keypad-btn.clear {
            background: #fee2e2;
            color: #dc2626;
            border-color: #fecaca;
        }

        .keypad-btn.enter {
            background: #dbeafe;
            color: #1d4ed8;
            border-color: #bfdbfe;
        }

        /* Animaciones */
        @keyframes slideUp {
            from { opacity: 0; transform: translateY(50px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }

        /* Contenido Principal - OCULTO INICIALMENTE */
        .container {
            max-width: 1400px;
            margin: 0 auto;
            opacity: 0;
            transition: opacity 0.5s ease;
            display: none;
        }

        /* Header */
        .header {
            background: white;
            padding: 15px 20px;
            border-radius: 8px;
            box-shadow: var(--card-shadow);
            margin-bottom: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-left: 3px solid var(--primary);
            position: relative;
        }

        .logout-btn {
            background: var(--light);
            color: var(--primary);
            border: none;
            border-radius: 6px;
            padding: 8px 16px;
            font-size: 0.9rem;
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .logout-btn:hover {
            background: #e5e7eb;
        }

        .header h1 {
            font-size: 18px;
            font-weight: 700;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .header h1 i {
            color: var(--primary);
        }

        /* Main Grid */
        .main-grid {
            display: grid;
            grid-template-columns: 350px 1fr;
            gap: 15px;
            margin-bottom: 15px;
        }

        @media (max-width: 1024px) {
            .main-grid {
                grid-template-columns: 1fr;
            }
        }

        /* Cards */
        .card {
            background: white;
            border-radius: 8px;
            padding: 20px;
            box-shadow: var(--card-shadow);
        }

        .card-title {
            font-size: 16px;
            margin-bottom: 15px;
            color: var(--dark);
            display: flex;
            align-items: center;
            gap: 8px;
            padding-bottom: 10px;
            border-bottom: 1px solid var(--border);
        }

        .card-title i {
            color: var(--primary);
        }

        /* Form */
        .form-group {
            margin-bottom: 12px;
        }

        .form-label {
            display: block;
            margin-bottom: 4px;
            font-weight: 600;
            color: var(--dark);
            font-size: 13px;
        }

        .form-control {
            width: 100%;
            padding: 8px 12px;
            border: 1px solid var(--border);
            border-radius: 6px;
            font-size: 14px;
            transition: var(--transition);
        }

        .form-control:focus {
            border-color: var(--primary);
            outline: none;
            box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.1);
        }

        .input-row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
        }

        /* Buttons */
        .btn {
            padding: 10px 16px;
            border-radius: 6px;
            font-weight: 600;
            font-size: 14px;
            border: none;
            cursor: pointer;
            transition: var(--transition);
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
        }

        .btn-primary {
            background: var(--primary);
            color: white;
        }

        .btn-primary:hover {
            background: var(--primary-dark);
        }

        .btn-success {
            background: var(--success);
            color: white;
        }

        .btn-danger {
            background: var(--danger);
            color: white;
        }

        .btn-sm {
            padding: 4px 8px;
            font-size: 12px;
        }

        /* KPI Grid */
        .kpi-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 8px;
            margin-bottom: 15px;
        }

        @media (max-width: 1024px) {
            .kpi-grid {
                grid-template-columns: repeat(3, 1fr);
            }
        }

        @media (max-width: 768px) {
            .kpi-grid {
                grid-template-columns: repeat(2, 1fr);
            }
        }

        @media (max-width: 480px) {
            .kpi-grid {
                grid-template-columns: 1fr;
            }
        }

        .kpi-card {
            background: white;
            padding: 12px;
            border-radius: 6px;
            border-left: 3px solid var(--primary);
            box-shadow: var(--card-shadow);
        }

        .kpi-card.scrap {
            border-left-color: var(--danger);
        }

        .kpi-card.hourly {
            border-left-color: var(--secondary);
        }

        .kpi-value {
            font-size: 20px;
            font-weight: 700;
            color: var(--dark);
            margin: 4px 0;
        }

        .kpi-label {
            font-size: 11px;
            color: var(--gray);
            text-transform: uppercase;
            font-weight: 600;
        }

        /* Pallet Tracker */
        .pallet-tracker {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
            gap: 10px;
            margin: 15px 0;
        }

        .pallet-card {
            background: white;
            padding: 12px;
            border-radius: 6px;
            border-top: 3px solid var(--primary);
            box-shadow: var(--card-shadow);
            position: relative;
            cursor: pointer;
            transition: var(--transition);
        }

        .pallet-card:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }

        .pallet-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 8px;
        }

        .pallet-name {
            font-size: 13px;
            font-weight: 600;
            color: var(--primary);
        }

        .delete-machine {
            position: absolute;
            top: 5px;
            right: 5px;
            background: none;
            border: none;
            color: var(--danger);
            cursor: pointer;
            font-size: 12px;
            opacity: 0.6;
            transition: var(--transition);
            z-index: 10;
        }

        .delete-machine:hover {
            opacity: 1;
        }

        .pallet-progress {
            height: 6px;
            background: #e5e7eb;
            border-radius: 3px;
            overflow: hidden;
            margin: 5px 0;
        }

        .pallet-fill {
            height: 100%;
            background: linear-gradient(90deg, var(--success), var(--primary));
            border-radius: 3px;
            transition: width 0.3s ease;
        }

        .pallet-stats {
            display: flex;
            justify-content: space-between;
            font-size: 11px;
            color: var(--gray);
        }

        /* History Cards Grid */
        .history-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 12px;
            margin-top: 15px;
        }

        @media (max-width: 768px) {
            .history-grid {
                grid-template-columns: 1fr;
            }
        }

        .history-card {
            background: white;
            border-radius: 8px;
            padding: 15px;
            box-shadow: var(--card-shadow);
            border-left: 4px solid var(--primary);
            transition: var(--transition);
            position: relative;
            cursor: pointer;
        }

        .history-card:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }

        .history-header {
            display: flex;
            justify-content: space-between;
            align-items: start;
            margin-bottom: 10px;
        }

        .history-machine {
            font-weight: 700;
            color: var(--primary);
            font-size: 14px;
        }

        .history-time {
            font-size: 11px;
            color: var(--gray);
            background: var(--light);
            padding: 2px 6px;
            border-radius: 10px;
        }

        .history-product {
            font-size: 13px;
            color: var(--dark);
            margin-bottom: 8px;
            font-weight: 600;
        }

        .history-stats {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 8px;
            margin: 10px 0;
        }

        @media (max-width: 480px) {
            .history-stats {
                grid-template-columns: repeat(3, 1fr);
            }
        }

        .stat-item {
            text-align: center;
        }

        .stat-value {
            font-size: 16px;
            font-weight: 700;
            color: var(--dark);
        }

        .stat-label {
            font-size: 10px;
            color: var(--gray);
            text-transform: uppercase;
        }

        .history-cycle {
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            color: var(--gray);
            margin-top: 10px;
            padding-top: 10px;
            border-top: 1px solid var(--border);
        }

        /* Scrap Display */
        .scrap-display {
            background: linear-gradient(135deg, #fef2f2, #fee2e2);
            border-radius: 6px;
            padding: 12px;
            text-align: center;
            margin: 10px 0;
            border: 1px solid #fecaca;
        }

        .scrap-value {
            font-size: 24px;
            font-weight: 800;
            color: var(--danger);
            margin: 5px 0;
        }

        /* Turno Info */
        .turno-info {
            background: linear-gradient(135deg, var(--primary), var(--primary-dark));
            color: white;
            padding: 12px;
            border-radius: 6px;
            margin-bottom: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        /* Quick Actions */
        .quick-actions {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
            margin-top: 15px;
        }

        /* Tabs */
        .tabs {
            display: flex;
            gap: 5px;
            margin-bottom: 15px;
            border-bottom: 1px solid var(--border);
            padding-bottom: 5px;
            overflow-x: auto;
        }

        .tab {
            padding: 8px 16px;
            background: none;
            border: none;
            color: var(--gray);
            font-weight: 600;
            cursor: pointer;
            border-radius: 4px;
            font-size: 13px;
            white-space: nowrap;
        }

        .tab.active {
            background: var(--primary);
            color: white;
        }

        /* Time Display */
        .time-display {
            font-size: 11px;
            color: var(--gray);
            display: flex;
            align-items: center;
            gap: 5px;
        }

        /* Modal */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 1000;
            justify-content: center;
            align-items: center;
            padding: 10px;
        }

        .modal-content {
            background: white;
            border-radius: 12px;
            width: 100%;
            max-width: 800px;
            max-height: 90vh;
            overflow-y: auto;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
        }

        .modal-header {
            background: linear-gradient(135deg, var(--primary), var(--primary-dark));
            color: white;
            padding: 20px;
            border-radius: 12px 12px 0 0;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .modal-header h2 {
            font-size: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .close-modal {
            background: none;
            border: none;
            color: white;
            font-size: 24px;
            cursor: pointer;
            padding: 0;
            width: 30px;
            height: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            transition: var(--transition);
        }

        .close-modal:hover {
            background: rgba(255,255,255,0.2);
        }

        .modal-body {
            padding: 25px;
        }

        .modal-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 20px;
        }

        @media (max-width: 768px) {
            .modal-grid {
                grid-template-columns: 1fr;
            }
        }

        .modal-section {
            margin-bottom: 25px;
        }

        .modal-section-title {
            font-size: 16px;
            font-weight: 700;
            color: var(--primary);
            margin-bottom: 15px;
            padding-bottom: 8px;
            border-bottom: 2px solid var(--border);
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .modal-kpi {
            background: var(--light);
            border-radius: 8px;
            padding: 15px;
            margin: 15px 0;
        }

        .modal-kpi-grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 15px;
            margin-top: 10px;
        }

        @media (max-width: 768px) {
            .modal-kpi-grid {
                grid-template-columns: repeat(2, 1fr);
            }
        }

        .modal-kpi-item {
            text-align: center;
        }

        .modal-kpi-value {
            font-size: 24px;
            font-weight: 800;
            color: var(--dark);
            margin: 5px 0;
        }

        .modal-kpi-label {
            font-size: 11px;
            color: var(--gray);
            text-transform: uppercase;
            font-weight: 600;
        }

        .modal-actions {
            display: flex;
            gap: 10px;
            margin-top: 25px;
            padding-top: 20px;
            border-top: 1px solid var(--border);
            flex-wrap: wrap;
        }

        .hourly-production {
            background: linear-gradient(135deg, #f0f9ff, #e0f2fe);
            border: 2px solid var(--secondary);
            border-radius: 10px;
            padding: 15px;
            text-align: center;
            margin: 15px 0;
        }

        .hourly-value {
            font-size: 32px;
            font-weight: 900;
            color: var(--secondary);
            margin: 10px 0;
        }

        /* Cycle Input Highlight */
        .cycle-input-group {
            position: relative;
        }

        .cycle-unit {
            position: absolute;
            right: 10px;
            top: 32px;
            color: var(--gray);
            font-size: 12px;
        }

        /* Responsive adicional */
        @media (max-width: 480px) {
            .header {
                flex-direction: column;
                align-items: flex-start;
                gap: 10px;
            }
            
            .logout-btn {
                align-self: flex-end;
            }
            
            .input-row {
                grid-template-columns: 1fr;
            }
            
            .quick-actions {
                grid-template-columns: 1fr;
            }
            
            .modal-actions {
                flex-direction: column;
            }
        }
    </style>
</head>
<body>
    <!-- PANTALLA DE LOGIN -->
    <div class="login-container" id="loginContainer">
        <div class="login-box">
            <div class="login-logo">
                <i class="fas fa-industry"></i>
            </div>
            <h1>PROD CONTROL - Acceso</h1>
            <p>Control de producción con scrap 3%<br>Ingrese la contraseña para continuar</p>
            
            <input type="password" id="passwordInput" class="login-input" 
                   placeholder="••••" maxlength="4" inputmode="numeric" autocomplete="off">
            
            <div class="login-error" id="loginError">
                <i class="fas fa-exclamation-triangle"></i> Contraseña incorrecta
            </div>
            
            <button class="login-btn" onclick="checkPassword()">
                <i class="fas fa-lock"></i> Ingresar al Sistema
            </button>
            
            <!-- Teclado numérico para tablets/móviles -->
            <div class="keypad">
                <button class="keypad-btn" onclick="addToPassword('1')">1</button>
                <button class="keypad-btn" onclick="addToPassword('2')">2</button>
                <button class="keypad-btn" onclick="addToPassword('3')">3</button>
                <button class="keypad-btn" onclick="addToPassword('4')">4</button>
                <button class="keypad-btn" onclick="addToPassword('5')">5</button>
                <button class="keypad-btn" onclick="addToPassword('6')">6</button>
                <button class="keypad-btn" onclick="addToPassword('7')">7</button>
                <button class="keypad-btn" onclick="addToPassword('8')">8</button>
                <button class="keypad-btn" onclick="addToPassword('9')">9</button>
                <button class="keypad-btn clear" onclick="clearPassword()">
                    <i class="fas fa-backspace"></i>
                </button>
                <button class="keypad-btn" onclick="addToPassword('0')">0</button>
                <button class="keypad-btn enter" onclick="checkPassword()">
                    <i class="fas fa-check"></i>
                </button>
            </div>
            
            <div class="password-hint">
                <strong>Contraseña:</strong> 0107<br>
                <small style="color: #666;">Sistema optimizado para tablet y celular</small>
            </div>
        </div>
    </div>

    <!-- CONTENIDO PRINCIPAL (OCULTO INICIALMENTE) -->
    <div class="container" id="mainContainer">
        <!-- Header -->
        <div class="header">
            <h1><i class="fas fa-industry"></i> PROD CONTROL - Scrap 3%</h1>
            <div style="display: flex; align-items: center; gap: 15px;">
                <div class="time-display">
                    <i class="fas fa-clock"></i>
                    <span id="currentTime">00:00:00</span>
                    <span style="background: var(--light); color: var(--danger); padding: 2px 6px; border-radius: 10px; font-size: 11px;">
                        SCRAP 3%
                    </span>
                </div>
                <button class="logout-btn" onclick="logout()">
                    <i class="fas fa-sign-out-alt"></i> Cerrar Sesión
                </button>
            </div>
        </div>

        <!-- Main Content Grid -->
        <div class="main-grid">
            <!-- Panel de Control -->
            <div class="card">
                <div class="card-title"><i class="fas fa-edit"></i> Nueva Producción</div>
                
                <div class="form-group">
                    <label class="form-label">Máquina</label>
                    <input type="text" id="machineNumber" class="form-control" value="M-001" placeholder="Ej: M-001">
                </div>

                <div class="form-group">
                    <label class="form-label">Producto</label>
                    <input type="text" id="productName" class="form-control" value="Caja 250ml" placeholder="Nombre del producto">
                </div>

                <div class="input-row">
                    <div class="form-group cycle-input-group">
                        <label class="form-label">Ciclo Inicial (segundos)</label>
                        <input type="number" id="cycleInitial" class="form-control" value="25.5" min="0.1" step="0.01" placeholder="Ej: 2.4">
                        <span class="cycle-unit">seg</span>
                    </div>
                    <div class="form-group cycle-input-group">
                        <label class="form-label">Ciclo Final (segundos)</label>
                        <input type="number" id="cycleFinal" class="form-control" value="23.8" min="0.1" step="0.01" placeholder="Ej: 2.2">
                        <span class="cycle-unit">seg</span>
                    </div>
                </div>

                <div class="input-row">
                    <div class="form-group">
                        <label class="form-label">Cavidades</label>
                        <input type="number" id="cavities" class="form-control" value="8" min="1">
                    </div>
                    <div class="form-group">
                        <label class="form-label">Scrap</label>
                        <input type="text" id="scrap" class="form-control" value="3%" readonly>
                    </div>
                </div>

                <div class="input-row">
                    <div class="form-group">
                        <label class="form-label">Pz/Caja</label>
                        <input type="number" id="piecesPerBox" class="form-control" value="100" min="1">
                    </div>
                    <div class="form-group">
                        <label class="form-label">Cx/Tarima</label>
                        <input type="number" id="boxesPerPallet" class="form-control" value="50" min="1">
                    </div>
                </div>

                <div class="form-group">
                    <label class="form-label">Horas Turno</label>
                    <input type="number" id="shiftHours" class="form-control" value="8" min="1" max="12" step="0.5">
                </div>

                <div class="quick-actions">
                    <button class="btn btn-primary" onclick="calculateProduction()">
                        <i class="fas fa-calculator"></i> Calcular
                    </button>
                    <button class="btn btn-success" onclick="saveProduction()">
                        <i class="fas fa-save"></i> Guardar
                    </button>
                </div>
            </div>

            <!-- Resultados y Seguimiento -->
            <div>
                <!-- KPI con Scrap y Producción por Hora -->
                <div class="kpi-grid">
                    <div class="kpi-card">
                        <div class="kpi-label">Tarimas/Turno</div>
                        <div class="kpi-value" id="palletsPerShift">0</div>
                    </div>
                    
                    <div class="kpi-card">
                        <div class="kpi-label">Tiempo/Tarima</div>
                        <div class="kpi-value" id="timePerPallet">0m</div>
                    </div>
                    
                    <div class="kpi-card">
                        <div class="kpi-label">Producción/Turno</div>
                        <div class="kpi-value" id="prodPerShift">0</div>
                    </div>
                    
                    <div class="kpi-card hourly">
                        <div class="kpi-label">Producción/Hora</div>
                        <div class="kpi-value" id="prodPerHour">0 pz</div>
                    </div>
                    
                    <div class="kpi-card scrap">
                        <div class="kpi-label">Scrap/Hora</div>
                        <div class="kpi-value" id="scrapPerHour">0 pz</div>
                    </div>
                </div>

                <!-- Display de Scrap y Producción por Hora -->
                <div class="scrap-display">
                    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px;">
                        <div>
                            <div style="font-size: 12px; color: var(--danger);">DESPERDICIO POR HORA</div>
                            <div class="scrap-value" id="scrapDisplay">0</div>
                            <div style="font-size: 11px; color: #92400e;">
                                <i class="fas fa-exclamation-triangle"></i> 
                                Se pierden <span id="scrapHourly">0</span> pz/h por scrap
                            </div>
                        </div>
                        <div>
                            <div style="font-size: 12px; color: var(--secondary);">PRODUCCIÓN POR HORA</div>
                            <div class="scrap-value" style="color: var(--secondary);" id="hourlyProdDisplay">0</div>
                            <div style="font-size: 11px; color: #0369a1;">
                                <i class="fas fa-bolt"></i> 
                                Producción real: <span id="realHourlyDisplay">0</span> pz/h
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Pallet Tracker por Máquina -->
                <div class="card">
                    <div class="card-title">
                        <i class="fas fa-pallet"></i> Avance de Tarimas
                        <span style="font-size: 11px; color: var(--gray); margin-left: auto;">
                            Toca máquina para editar
                        </span>
                    </div>
                    <div class="pallet-tracker" id="palletTracker">
                        <!-- Se generan dinámicamente -->
                    </div>
                </div>

                <!-- Turno Info -->
                <div class="turno-info">
                    <div>
                        <div style="font-size: 12px; opacity: 0.9;">TURNO ACTIVO</div>
                        <div style="font-size: 16px; font-weight: 700;">Turno <span id="shiftNumber">1</span></div>
                    </div>
                    <button class="btn" onclick="closeShift()" style="background: rgba(255,255,255,0.2); color: white; padding: 8px 12px;">
                        <i class="fas fa-flag-checkered"></i> Cerrar
                    </button>
                </div>
            </div>
        </div>

        <!-- Historial en Cuadrícula -->
        <div class="card">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px;">
                <div class="card-title"><i class="fas fa-history"></i> Seguimiento de Producción</div>
                <div style="display: flex; gap: 8px;">
                    <button class="btn" onclick="clearAll()" style="background: var(--light); color: var(--danger); padding: 6px 12px;">
                        <i class="fas fa-trash"></i> Limpiar Todo
                    </button>
                    <button class="btn" onclick="exportData()" style="background: var(--light); color: var(--primary); padding: 6px 12px;">
                        <i class="fas fa-file-export"></i> Exportar
                    </button>
                </div>
            </div>

            <!-- Tabs -->
            <div class="tabs">
                <button class="tab active" onclick="showTab('today')">Hoy</button>
                <button class="tab" onclick="showTab('all')">Todos</button>
                <button class="tab" onclick="showTab('byMachine')">Por Máquina</button>
            </div>

            <!-- Cuadrícula de Historial -->
            <div class="history-grid" id="historyGrid">
                <!-- Se generan tarjetas dinámicamente -->
            </div>

            <!-- Totales -->
            <div style="margin-top: 20px; padding: 15px; background: var(--light); border-radius: 6px;">
                <div style="display: grid; grid-template-columns: repeat(5, 1fr); gap: 10px; text-align: center;">
                    <div>
                        <div style="font-size: 11px; color: var(--gray);">PIEZAS HOY</div>
                        <div style="font-size: 16px; font-weight: 700; color: var(--primary);" id="totalTodayPieces">0</div>
                    </div>
                    <div>
                        <div style="font-size: 11px; color: var(--gray);">SCRAP HOY</div>
                        <div style="font-size: 16px; font-weight: 700; color: var(--danger);" id="totalTodayScrap">0 pz</div>
                    </div>
                    <div>
                        <div style="font-size: 11px; color: var(--gray);">PROD/HORA</div>
                        <div style="font-size: 16px; font-weight: 700; color: var(--secondary);" id="avgHourlyToday">0 pz</div>
                    </div>
                    <div>
                        <div style="font-size: 11px; color: var(--gray);">MÁQUINAS</div>
                        <div style="font-size: 16px; font-weight: 700; color: var(--warning);" id="machinesToday">0</div>
                    </div>
                    <div>
                        <div style="font-size: 11px; color: var(--gray);">TARIMAS</div>
                        <div style="font-size: 16px; font-weight: 700; color: var(--success);" id="totalTodayPallets">0</div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal para editar máquina -->
    <div id="machineModal" class="modal">
        <div class="modal-content">
            <div class="modal-header">
                <h2><i class="fas fa-edit"></i> Editar Parámetros de Producción</h2>
                <button class="close-modal" onclick="closeModal()">&times;</button>
            </div>
            <div class="modal-body">
                <div class="modal-grid">
                    <div class="modal-section">
                        <div class="modal-section-title"><i class="fas fa-cog"></i> Configuración Básica</div>
                        
                        <div class="form-group">
                            <label class="form-label">Máquina</label>
                            <input type="text" id="modalMachine" class="form-control">
                        </div>

                        <div class="form-group">
                            <label class="form-label">Producto</label>
                            <input type="text" id="modalProduct" class="form-control">
                        </div>

                        <div class="input-row">
                            <div class="form-group cycle-input-group">
                                <label class="form-label">Ciclo Inicial (segundos)</label>
                                <input type="number" id="modalCycleInit" class="form-control" min="0.1" step="0.01">
                                <span class="cycle-unit">seg</span>
                            </div>
                            <div class="form-group cycle-input-group">
                                <label class="form-label">Ciclo Final (segundos)</label>
                                <input type="number" id="modalCycleFinal" class="form-control" min="0.1" step="0.01">
                                <span class="cycle-unit">seg</span>
                            </div>
                        </div>

                        <div class="input-row">
                            <div class="form-group">
                                <label class="form-label">Cavidades</label>
                                <input type="number" id="modalCavities" class="form-control" min="1">
                            </div>
                            <div class="form-group">
                                <label class="form-label">Scrap</label>
                                <input type="text" id="modalScrap" class="form-control" value="3%" readonly>
                            </div>
                        </div>
                    </div>

                    <div class="modal-section">
                        <div class="modal-section-title"><i class="fas fa-box"></i> Empaque y Turno</div>
                        
                        <div class="input-row">
                            <div class="form-group">
                                <label class="form-label">Pz/Caja</label>
                                <input type="number" id="modalPiecesPerBox" class="form-control" min="1">
                            </div>
                            <div class="form-group">
                                <label class="form-label">Cx/Tarima</label>
                                <input type="number" id="modalBoxesPerPallet" class="form-control" min="1">
                            </div>
                        </div>

                        <div class="form-group">
                            <label class="form-label">Horas Turno</label>
                            <input type="number" id="modalShiftHours" class="form-control" min="1" max="12" step="0.5">
                        </div>

                        <div class="form-group">
                            <label class="form-label">Operador</label>
                            <input type="text" id="modalOperator" class="form-control">
                        </div>

                        <div class="modal-kpi">
                            <div class="modal-section-title"><i class="fas fa-chart-line"></i> Resultados en Tiempo Real</div>
                            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 15px;">
                                <div style="text-align: center;">
                                    <div style="font-size: 11px; color: var(--gray);">PRODUCCIÓN POR HORA</div>
                                    <div id="modalHourlyProd" style="font-size: 24px; font-weight: 800; color: var(--secondary);">0 pz</div>
                                </div>
                                <div style="text-align: center;">
                                    <div style="font-size: 11px; color: var(--gray);">SCRAP POR HORA</div>
                                    <div id="modalHourlyScrap" style="font-size: 24px; font-weight: 800; color: var(--danger);">0 pz</div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- KPIs detallados -->
                <div class="modal-kpi">
                    <div class="modal-section-title"><i class="fas fa-calculator"></i> Cálculos Detallados</div>
                    <div class="modal-kpi-grid">
                        <div class="modal-kpi-item">
                            <div class="modal-kpi-label">Ciclo Promedio</div>
                            <div class="modal-kpi-value" id="modalAvgCycle">0.0s</div>
                        </div>
                        <div class="modal-kpi-item">
                            <div class="modal-kpi-label">Ciclos/Hora</div>
                            <div class="modal-kpi-value" id="modalCyclesPerHour">0</div>
                        </div>
                        <div class="modal-kpi-item">
                            <div class="modal-kpi-label">Pz Teóricas/H</div>
                            <div class="modal-kpi-value" id="modalTheoreticalHourly">0</div>
                        </div>
                        <div class="modal-kpi-item">
                            <div class="modal-kpi-label">Pz Reales/H</div>
                            <div class="modal-kpi-value" id="modalRealHourly">0</div>
                        </div>
                    </div>
                </div>

                <!-- Producción por hora destacada -->
                <div class="hourly-production">
                    <div style="font-size: 14px; color: var(--secondary); font-weight: 600;">
                        <i class="fas fa-bolt"></i> PRODUCCIÓN POR HORA
                    </div>
                    <div id="modalProductionHour" class="hourly-value">0 pz/h</div>
                    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-top: 15px;">
                        <div>
                            <div style="font-size: 11px; color: var(--gray);">PRODUCCIÓN POR TURNO</div>
                            <div id="modalShiftProduction" style="font-size: 18px; font-weight: 700; color: var(--dark);">0 pz</div>
                        </div>
                        <div>
                            <div style="font-size: 11px; color: var(--gray);">TIEMPO POR TARIMA</div>
                            <div id="modalTimePerPallet" style="font-size: 18px; font-weight: 700; color: var(--dark);">0m</div>
                        </div>
                    </div>
                </div>

                <!-- Acciones del modal -->
                <div class="modal-actions">
                    <button class="btn btn-primary" onclick="recalculateModal()" style="flex: 1;">
                        <i class="fas fa-sync-alt"></i> Recalcular
                    </button>
                    <button class="btn btn-success" onclick="saveModalChanges()" style="flex: 1;">
                        <i class="fas fa-save"></i> Guardar Cambios
                    </button>
                    <button class="btn" onclick="closeModal()" style="background: var(--light); color: var(--gray);">
                        Cancelar
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // =============================================
        // SISTEMA DE LOGIN CON CONTRASEÑA 0107
        // =============================================
        const VALID_PASSWORD = "0107";
        const SESSION_KEY = 'prodcontrol_logged_in';
        
        let isLoggedIn = false;
        
        // Funciones del teclado numérico
        function addToPassword(number) {
            const input = document.getElementById('passwordInput');
            if (input.value.length < 4) {
                input.value += number;
                // Animar el input
                input.style.transform = 'scale(1.05)';
                setTimeout(() => {
                    input.style.transform = 'scale(1)';
                }, 150);
            }
        }
        
        function clearPassword() {
            document.getElementById('passwordInput').value = '';
        }
        
        function checkPassword() {
            const passwordInput = document.getElementById('passwordInput');
            const loginError = document.getElementById('loginError');
            const enteredPassword = passwordInput.value.trim();
            
            if (enteredPassword === VALID_PASSWORD) {
                // Contraseña correcta
                loginError.style.display = 'none';
                isLoggedIn = true;
                
                // Guardar sesión en localStorage
                localStorage.setItem(SESSION_KEY, 'true');
                
                // Mostrar contenido principal
                showMainContent();
            } else {
                // Contraseña incorrecta
                loginError.style.display = 'block';
                passwordInput.value = '';
                passwordInput.focus();
                
                // Efecto de error
                passwordInput.style.borderColor = '#f44336';
                passwordInput.style.boxShadow = '0 0 0 3px rgba(244, 67, 54, 0.1)';
                
                setTimeout(() => {
                    passwordInput.style.borderColor = '';
                    passwordInput.style.boxShadow = '';
                }, 1000);
                
                // Vibrar en dispositivos móviles
                if (navigator.vibrate) {
                    navigator.vibrate([100, 50, 100]);
                }
            }
        }
        
        function showMainContent() {
            const loginContainer = document.getElementById('loginContainer');
            const mainContainer = document.getElementById('mainContainer');
            
            // Ocultar login con animación
            loginContainer.style.opacity = '0';
            
            setTimeout(() => {
                loginContainer.style.display = 'none';
                mainContainer.style.display = 'block';
                
                // Inicializar aplicación
                setTimeout(() => {
                    mainContainer.style.opacity = '1';
                    initApp();
                }, 50);
            }, 500);
        }
        
        function logout() {
            if (confirm('¿Estás seguro de que quieres cerrar sesión?')) {
                // Eliminar sesión
                localStorage.removeItem(SESSION_KEY);
                isLoggedIn = false;
                
                // Ocultar contenido principal
                const loginContainer = document.getElementById('loginContainer');
                const mainContainer = document.getElementById('mainContainer');
                
                mainContainer.style.opacity = '0';
                
                setTimeout(() => {
                    mainContainer.style.display = 'none';
                    loginContainer.style.display = 'flex';
                    
                    setTimeout(() => {
                        loginContainer.style.opacity = '1';
                        clearPassword();
                        document.getElementById('passwordInput').focus();
                    }, 50);
                }, 500);
            }
        }
        
        function checkLoginStatus() {
            const savedLogin = localStorage.getItem(SESSION_KEY);
            
            if (savedLogin === 'true') {
                // Sesión válida
                isLoggedIn = true;
                showMainContent();
            } else {
                // Mostrar login
                document.getElementById('passwordInput').focus();
                
                // Permitir Enter para enviar
                document.getElementById('passwordInput').addEventListener('keypress', function(e) {
                    if (e.key === 'Enter') {
                        checkPassword();
                    }
                });
            }
        }
        
        // =============================================
        // CÓDIGO ORIGINAL DEL SISTEMA PROD CONTROL
        // =============================================
        
        // Variables globales
        let productionHistory = [];
        let scrapPercentage = 3;
        let currentEditingRecord = null;

        // Inicializar aplicación
        function initApp() {
            updateDateTime();
            setInterval(updateDateTime, 1000);
            calculateProduction();
            
            // Cargar datos guardados
            try {
                const savedData = localStorage.getItem('productionHistory');
                if (savedData) {
                    productionHistory = JSON.parse(savedData);
                    updateHistoryGrid();
                    updatePalletTracker();
                } else {
                    loadSampleData();
                }
            } catch (e) {
                console.error('Error cargando datos:', e);
                loadSampleData();
            }
        }

        // Permitir decimales en input
        function allowDecimal(event) {
            const charCode = event.which ? event.which : event.keyCode;
            if (charCode === 46 || charCode === 44) { // Punto o coma
                return true;
            }
            if (charCode > 31 && (charCode < 48 || charCode > 57)) {
                return false;
            }
            return true;
        }

        // Actualizar fecha y hora
        function updateDateTime() {
            const now = new Date();
            document.getElementById('currentTime').textContent = 
                now.toLocaleTimeString('es-ES', {hour: '2-digit', minute: '2-digit', second: '2-digit'});
        }

        // Calcular producción con scrap por hora
        function calculateProduction() {
            try {
                // Obtener valores
                const machine = document.getElementById('machineNumber').value || 'M-001';
                const product = document.getElementById('productName').value || 'Producto';
                const cycleInit = parseFloat(document.getElementById('cycleInitial').value.replace(',', '.')) || 25.5;
                const cycleFinal = parseFloat(document.getElementById('cycleFinal').value.replace(',', '.')) || 23.8;
                const cavities = parseInt(document.getElementById('cavities').value) || 8;
                const piecesPerBox = parseInt(document.getElementById('piecesPerBox').value) || 100;
                const boxesPerPallet = parseInt(document.getElementById('boxesPerPallet').value) || 50;
                const shiftHours = parseFloat(document.getElementById('shiftHours').value) || 8;
                
                // Calcular ciclo promedio
                const avgCycle = (cycleInit + cycleFinal) / 2;
                
                // Cálculos base
                const cyclesPerHour = 3600 / avgCycle;
                const theoreticalHourly = cyclesPerHour * cavities; // Producción teórica por hora
                
                // Cálculo de SCRAP por hora (3%)
                const scrapPerHour = Math.round(theoreticalHourly * (scrapPercentage / 100));
                
                // Producción real (con scrap descontado)
                const realHourly = Math.round(theoreticalHourly * (1 - scrapPercentage / 100));
                const shiftProduction = Math.round(realHourly * shiftHours);
                
                // Cálculos de cajas y tarimas
                const boxesPerShift = Math.floor(shiftProduction / piecesPerBox);
                const palletsPerShift = Math.floor(boxesPerShift / boxesPerPallet);
                
                // Cálculo de tiempo por tarima
                const shiftMinutes = shiftHours * 60;
                let timePerPallet = 0;
                let timePerPalletText = "N/A";
                
                if (palletsPerShift > 0) {
                    timePerPallet = shiftMinutes / palletsPerShift;
                    timePerPalletText = timePerPallet >= 60 ? 
                        `${Math.floor(timePerPallet / 60)}h ${Math.round(timePerPallet % 60)}m` : 
                        `${Math.round(timePerPallet)}m`;
                }
                
                // Calcular scrap total del turno
                const scrapPerShift = Math.round(scrapPerHour * shiftHours);
                
                // Actualizar UI - INCLUYENDO PRODUCCIÓN POR HORA
                document.getElementById('prodPerShift').textContent = shiftProduction.toLocaleString();
                document.getElementById('prodPerHour').textContent = realHourly.toLocaleString() + ' pz';
                document.getElementById('palletsPerShift').textContent = palletsPerShift;
                document.getElementById('timePerPallet').textContent = timePerPalletText;
                document.getElementById('scrapPerHour').textContent = scrapPerHour + ' pz';
                document.getElementById('scrapDisplay').textContent = scrapPerHour + ' pz/h';
                document.getElementById('scrapHourly').textContent = scrapPerHour;
                document.getElementById('hourlyProdDisplay').textContent = realHourly.toLocaleString() + ' pz/h';
                document.getElementById('realHourlyDisplay').textContent = realHourly.toLocaleString();
                
                return {
                    machine: machine,
                    product: product,
                    cycleInit: cycleInit,
                    cycleFinal: cycleFinal,
                    cavities: cavities,
                    piecesPerShift: shiftProduction,
                    boxesPerShift: boxesPerShift,
                    palletsPerShift: palletsPerShift,
                    scrapPerHour: scrapPerHour,
                    scrapPerShift: scrapPerShift,
                    timePerPallet: timePerPallet,
                    timePerPalletText: timePerPalletText,
                    theoreticalHourly: Math.round(theoreticalHourly),
                    realHourly: realHourly,
                    cyclesPerHour: Math.round(cyclesPerHour),
                    avgCycle: avgCycle,
                    piecesPerBox: piecesPerBox,
                    boxesPerPallet: boxesPerPallet,
                    shiftHours: shiftHours
                };
            } catch (error) {
                console.error('Error en cálculo:', error);
                showMessage('❌ Error en los datos de entrada', 'error');
                return null;
            }
        }

        // Guardar producción
        function saveProduction() {
            try {
                const results = calculateProduction();
                if (!results) return;
                
                const now = new Date();
                
                const record = {
                    id: Date.now(),
                    time: now.toLocaleTimeString('es-ES', {hour: '2-digit', minute: '2-digit'}),
                    date: now.toLocaleDateString('es-ES'),
                    machine: results.machine,
                    product: results.product,
                    cycleInit: results.cycleInit,
                    cycleFinal: results.cycleFinal,
                    cavities: results.cavities,
                    piecesPerShift: results.piecesPerShift,
                    boxesPerShift: results.boxesPerShift,
                    palletsPerShift: results.palletsPerShift,
                    scrapPerHour: results.scrapPerHour,
                    scrapPerShift: results.scrapPerShift,
                    theoreticalHourly: results.theoreticalHourly,
                    realHourly: results.realHourly,
                    timePerPallet: results.timePerPalletText,
                    cyclesPerHour: results.cyclesPerHour,
                    avgCycle: results.avgCycle,
                    piecesPerBox: results.piecesPerBox,
                    boxesPerPallet: results.boxesPerPallet,
                    shiftHours: results.shiftHours,
                    operator: `OP${Math.floor(Math.random() * 5) + 1}`,
                    status: 'active'
                };
                
                // Agregar al historial
                productionHistory.unshift(record);
                
                // Guardar en localStorage
                localStorage.setItem('productionHistory', JSON.stringify(productionHistory));
                
                // Actualizar vistas
                updateHistoryGrid();
                updatePalletTracker();
                updateTotals();
                
                // Feedback
                showMessage(`✅ ${record.machine} guardada | ${record.realHourly} pz/h | Scrap: ${record.scrapPerHour} pz/h`, 'success');
            } catch (error) {
                console.error('Error guardando:', error);
                showMessage('❌ Error al guardar producción', 'error');
            }
        }

        // Actualizar cuadrícula de historial
        function updateHistoryGrid(filter = 'today') {
            try {
                const grid = document.getElementById('historyGrid');
                grid.innerHTML = '';
                
                if (productionHistory.length === 0) {
                    grid.innerHTML = '<div style="grid-column: 1/-1; text-align: center; padding: 40px; color: var(--gray);">No hay registros de producción</div>';
                    return;
                }
                
                const today = new Date().toLocaleDateString('es-ES');
                let recordsToShow = [];
                
                if (filter === 'today') {
                    recordsToShow = productionHistory.filter(r => r.date === today);
                } else if (filter === 'all') {
                    recordsToShow = productionHistory.slice(0, 12);
                } else if (filter === 'byMachine') {
                    // Agrupar por máquina (último registro de cada máquina)
                    const machineMap = new Map();
                    productionHistory.forEach(record => {
                        if (!machineMap.has(record.machine) || record.id > machineMap.get(record.machine).id) {
                            machineMap.set(record.machine, record);
                        }
                    });
                    recordsToShow = Array.from(machineMap.values()).slice(0, 12);
                }
                
                if (recordsToShow.length === 0) {
                    grid.innerHTML = '<div style="grid-column: 1/-1; text-align: center; padding: 40px; color: var(--gray);">No hay registros para este filtro</div>';
                    return;
                }
                
                recordsToShow.forEach(record => {
                    const card = document.createElement('div');
                    card.className = 'history-card';
                    card.innerHTML = `
                        <div class="history-header">
                            <div class="history-machine">${record.machine}</div>
                            <div class="history-time">${record.time}</div>
                            <button class="delete-machine" onclick="deleteRecord(${record.id})" style="position: absolute; top: 10px; right: 10px;">
                                <i class="fas fa-times"></i>
                            </button>
                        </div>
                        <div class="history-product">${record.product}</div>
                        <div class="history-stats">
                            <div class="stat-item">
                                <div class="stat-value">${record.palletsPerShift}</div>
                                <div class="stat-label">Tarimas</div>
                            </div>
                            <div class="stat-item">
                                <div class="stat-value">${record.piecesPerShift.toLocaleString()}</div>
                                <div class="stat-label">Piezas</div>
                            </div>
                            <div class="stat-item">
                                <div class="stat-value" style="color: var(--secondary);">${record.realHourly.toLocaleString()}</div>
                                <div class="stat-label">Pz/Hora</div>
                            </div>
                            <div class="stat-item">
                                <div class="stat-value" style="color: var(--danger);">${record.scrapPerHour}</div>
                                <div class="stat-label">Scrap/h</div>
                            </div>
                            <div class="stat-item">
                                <div class="stat-value">${record.boxesPerShift}</div>
                                <div class="stat-label">Cajas</div>
                            </div>
                        </div>
                        <div class="history-cycle">
                            <span>Ciclo: ${record.cycleInit.toFixed(1)}s → ${record.cycleFinal.toFixed(1)}s</span>
                            <span>${record.timePerPallet}</span>
                        </div>
                    `;
                    
                    // Click para abrir modal de edición
                    card.addEventListener('click', (e) => {
                        if (!e.target.closest('.delete-machine')) {
                            openEditModal(record);
                        }
                    });
                    grid.appendChild(card);
                });
            } catch (error) {
                console.error('Error actualizando historial:', error);
            }
        }

        // Mostrar pestaña
        function showTab(tabName) {
            try {
                document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
                event.target.classList.add('active');
                updateHistoryGrid(tabName);
            } catch (error) {
                console.error('Error cambiando pestaña:', error);
            }
        }

        // Actualizar tracker de tarimas por máquina
        function updatePalletTracker() {
            try {
                const tracker = document.getElementById('palletTracker');
                tracker.innerHTML = '';
                
                const today = new Date().toLocaleDateString('es-ES');
                const todayRecords = productionHistory.filter(r => r.date === today);
                
                if (todayRecords.length === 0) {
                    tracker.innerHTML = '<div style="grid-column: 1/-1; text-align: center; padding: 20px; color: var(--gray);">No hay máquinas activas hoy</div>';
                    return;
                }
                
                // Agrupar por máquina
                const machineGroups = {};
                todayRecords.forEach(record => {
                    if (!machineGroups[record.machine]) {
                        machineGroups[record.machine] = {
                            totalPallets: 0,
                            totalPieces: 0,
                            totalScrap: 0,
                            lastTime: record.time,
                            product: record.product,
                            count: 0,
                            realHourly: 0,
                            lastRecord: record
                        };
                    }
                    machineGroups[record.machine].totalPallets += record.palletsPerShift;
                    machineGroups[record.machine].totalPieces += record.piecesPerShift;
                    machineGroups[record.machine].totalScrap += record.scrapPerShift;
                    machineGroups[record.machine].lastTime = record.time;
                    machineGroups[record.machine].count += 1;
                    machineGroups[record.machine].realHourly = record.realHourly;
                    machineGroups[record.machine].lastRecord = record;
                });
                
                // Crear tarjetas de seguimiento
                Object.entries(machineGroups).forEach(([machine, data]) => {
                    const card = document.createElement('div');
                    card.className = 'pallet-card';
                    
                    // Calcular progreso (basado en turno de 8 horas)
                    const now = new Date();
                    const startHour = 8;
                    const currentHour = now.getHours();
                    const minutesSinceStart = Math.max(0, (currentHour - startHour) * 60 + now.getMinutes());
                    const progress = Math.min(100, (minutesSinceStart / 480) * 100);
                    
                    // Calcular scrap promedio por hora
                    const avgScrapPerHour = Math.round(data.totalScrap / data.count);
                    
                    card.innerHTML = `
                        <button class="delete-machine" onclick="deleteMachine('${machine}')">
                            <i class="fas fa-times"></i>
                        </button>
                        <div class="pallet-header">
                            <div class="pallet-name">${machine}</div>
                            <div style="font-size: 12px; font-weight: 700; color: var(--secondary);">${data.realHourly.toLocaleString()} pz/h</div>
                        </div>
                        <div style="font-size: 11px; color: var(--gray); margin-bottom: 5px;">${data.product.substring(0, 15)}</div>
                        <div class="pallet-progress">
                            <div class="pallet-fill" style="width: ${progress}%"></div>
                        </div>
                        <div class="pallet-stats">
                            <span>${data.totalPieces.toLocaleString()} pz</span>
                            <span>${data.totalPallets} tar</span>
                        </div>
                        <div style="font-size: 10px; color: var(--danger); margin-top: 5px;">
                            <i class="fas fa-trash"></i> Scrap: ${data.totalScrap} pz total
                        </div>
                    `;
                    
                    // Click para abrir modal de edición con el último registro
                    card.addEventListener('click', (e) => {
                        if (!e.target.closest('.delete-machine')) {
                            openEditModal(data.lastRecord);
                        }
                    });
                    
                    tracker.appendChild(card);
                });
            } catch (error) {
                console.error('Error actualizando tracker:', error);
            }
        }

        // Abrir modal para editar máquina
        function openEditModal(record) {
            currentEditingRecord = record;
            
            // Llenar campos del modal
            document.getElementById('modalMachine').value = record.machine;
            document.getElementById('modalProduct').value = record.product;
            document.getElementById('modalCycleInit').value = record.cycleInit.toFixed(2);
            document.getElementById('modalCycleFinal').value = record.cycleFinal.toFixed(2);
            document.getElementById('modalCavities').value = record.cavities;
            document.getElementById('modalPiecesPerBox').value = record.piecesPerBox || 100;
            document.getElementById('modalBoxesPerPallet').value = record.boxesPerPallet || 50;
            document.getElementById('modalShiftHours').value = record.shiftHours || 8;
            document.getElementById('modalOperator').value = record.operator || `OP${Math.floor(Math.random() * 5) + 1}`;
            
            // Calcular y mostrar resultados iniciales
            recalculateModalValues();
            
            // Mostrar modal
            document.getElementById('machineModal').style.display = 'flex';
        }

        // Recalcular valores del modal
        function recalculateModalValues() {
            try {
                const cycleInit = parseFloat(document.getElementById('modalCycleInit').value.replace(',', '.')) || 0;
                const cycleFinal = parseFloat(document.getElementById('modalCycleFinal').value.replace(',', '.')) || 0;
                const cavities = parseInt(document.getElementById('modalCavities').value) || 0;
                const piecesPerBox = parseInt(document.getElementById('modalPiecesPerBox').value) || 100;
                const boxesPerPallet = parseInt(document.getElementById('modalBoxesPerPallet').value) || 50;
                const shiftHours = parseFloat(document.getElementById('modalShiftHours').value) || 8;
                
                if (cycleInit <= 0 || cycleFinal <= 0 || cavities <= 0) {
                    showMessage('❌ Complete los parámetros básicos', 'error');
                    return;
                }
                
                // Calcular ciclo promedio
                const avgCycle = (cycleInit + cycleFinal) / 2;
                
                // Cálculos base
                const cyclesPerHour = 3600 / avgCycle;
                const theoreticalHourly = cyclesPerHour * cavities;
                
                // Cálculo de SCRAP por hora (3%)
                const scrapPerHour = Math.round(theoreticalHourly * (scrapPercentage / 100));
                
                // Producción real (con scrap descontado)
                const realHourly = Math.round(theoreticalHourly * (1 - scrapPercentage / 100));
                const shiftProduction = Math.round(realHourly * shiftHours);
                
                // Cálculos de cajas y tarimas
                const boxesPerShift = Math.floor(shiftProduction / piecesPerBox);
                const palletsPerShift = Math.floor(boxesPerShift / boxesPerPallet);
                
                // Cálculo de tiempo por tarima
                const shiftMinutes = shiftHours * 60;
                let timePerPalletText = "N/A";
                
                if (palletsPerShift > 0) {
                    const timePerPallet = shiftMinutes / palletsPerShift;
                    timePerPalletText = timePerPallet >= 60 ? 
                        `${Math.floor(timePerPallet / 60)}h ${Math.round(timePerPallet % 60)}m` : 
                        `${Math.round(timePerPallet)}m`;
                }
                
                // Actualizar UI del modal
                document.getElementById('modalAvgCycle').textContent = avgCycle.toFixed(2) + 's';
                document.getElementById('modalCyclesPerHour').textContent = Math.round(cyclesPerHour);
                document.getElementById('modalTheoreticalHourly').textContent = Math.round(theoreticalHourly).toLocaleString();
                document.getElementById('modalRealHourly').textContent = realHourly.toLocaleString();
                document.getElementById('modalHourlyProd').textContent = realHourly.toLocaleString() + ' pz';
                document.getElementById('modalHourlyScrap').textContent = scrapPerHour + ' pz';
                document.getElementById('modalProductionHour').textContent = realHourly.toLocaleString() + ' pz/h';
                document.getElementById('modalShiftProduction').textContent = shiftProduction.toLocaleString() + ' pz';
                document.getElementById('modalTimePerPallet').textContent = timePerPalletText;
                
            } catch (error) {
                console.error('Error en recálculo del modal:', error);
                showMessage('❌ Error en los datos', 'error');
            }
        }

        // Recalcular en el modal (botón)
        function recalculateModal() {
            recalculateModalValues();
            showMessage('🔄 Valores recalculados', 'info');
        }

        // Guardar cambios del modal
        function saveModalChanges() {
            try {
                if (!currentEditingRecord) return;
                
                // Obtener valores del modal
                const machine = document.getElementById('modalMachine').value;
                const product = document.getElementById('modalProduct').value;
                const cycleInit = parseFloat(document.getElementById('modalCycleInit').value.replace(',', '.'));
                const cycleFinal = parseFloat(document.getElementById('modalCycleFinal').value.replace(',', '.'));
                const cavities = parseInt(document.getElementById('modalCavities').value);
                const piecesPerBox = parseInt(document.getElementById('modalPiecesPerBox').value);
                const boxesPerPallet = parseInt(document.getElementById('modalBoxesPerPallet').value);
                const shiftHours = parseFloat(document.getElementById('modalShiftHours').value);
                const operator = document.getElementById('modalOperator').value;
                
                // Recalcular con nuevos valores
                const avgCycle = (cycleInit + cycleFinal) / 2;
                const cyclesPerHour = 3600 / avgCycle;
                const theoreticalHourly = cyclesPerHour * cavities;
                const scrapPerHour = Math.round(theoreticalHourly * (scrapPercentage / 100));
                const realHourly = Math.round(theoreticalHourly * (1 - scrapPercentage / 100));
                const shiftProduction = Math.round(realHourly * shiftHours);
                const boxesPerShift = Math.floor(shiftProduction / piecesPerBox);
                const palletsPerShift = Math.floor(boxesPerShift / boxesPerPallet);
                
                // Calcular tiempo por tarima
                const shiftMinutes = shiftHours * 60;
                let timePerPalletText = "N/A";
                if (palletsPerShift > 0) {
                    const timePerPallet = shiftMinutes / palletsPerShift;
                    timePerPalletText = timePerPallet >= 60 ? 
                        `${Math.floor(timePerPallet / 60)}h ${Math.round(timePerPallet % 60)}m` : 
                        `${Math.round(timePerPallet)}m`;
                }
                
                // Actualizar registro
                currentEditingRecord.machine = machine;
                currentEditingRecord.product = product;
                currentEditingRecord.cycleInit = cycleInit;
                currentEditingRecord.cycleFinal = cycleFinal;
                currentEditingRecord.cavities = cavities;
                currentEditingRecord.piecesPerShift = shiftProduction;
                currentEditingRecord.boxesPerShift = boxesPerShift;
                currentEditingRecord.palletsPerShift = palletsPerShift;
                currentEditingRecord.scrapPerHour = scrapPerHour;
                currentEditingRecord.scrapPerShift = Math.round(scrapPerHour * shiftHours);
                currentEditingRecord.theoreticalHourly = Math.round(theoreticalHourly);
                currentEditingRecord.realHourly = realHourly;
                currentEditingRecord.timePerPallet = timePerPalletText;
                currentEditingRecord.cyclesPerHour = Math.round(cyclesPerHour);
                currentEditingRecord.avgCycle = avgCycle;
                currentEditingRecord.piecesPerBox = piecesPerBox;
                currentEditingRecord.boxesPerPallet = boxesPerPallet;
                currentEditingRecord.shiftHours = shiftHours;
                currentEditingRecord.operator = operator;
                
                // Guardar en localStorage
                localStorage.setItem('productionHistory', JSON.stringify(productionHistory));
                
                // Actualizar vistas
                updateHistoryGrid();
                updatePalletTracker();
                updateTotals();
                
                // Cerrar modal
                closeModal();
                
                // Feedback
                showMessage(`✅ ${machine} actualizada | ${realHourly} pz/h | Scrap: ${scrapPerHour} pz/h`, 'success');
                
            } catch (error) {
                console.error('Error guardando cambios:', error);
                showMessage('❌ Error al guardar cambios', 'error');
            }
        }

        // Cerrar modal
        function closeModal() {
            document.getElementById('machineModal').style.display = 'none';
            currentEditingRecord = null;
        }

        // Eliminar registro individual
        function deleteRecord(id) {
            if (confirm('¿Eliminar este registro de producción?')) {
                productionHistory = productionHistory.filter(r => r.id !== id);
                localStorage.setItem('productionHistory', JSON.stringify(productionHistory));
                updateHistoryGrid();
                updatePalletTracker();
                updateTotals();
                showMessage('🗑️ Registro eliminado', 'info');
            }
        }

        // Eliminar todas las máquinas del mismo tipo
        function deleteMachine(machineName) {
            if (confirm(`¿Eliminar TODOS los registros de la máquina ${machineName}?`)) {
                productionHistory = productionHistory.filter(r => r.machine !== machineName);
                localStorage.setItem('productionHistory', JSON.stringify(productionHistory));
                updateHistoryGrid();
                updatePalletTracker();
                updateTotals();
                showMessage(`🗑️ Máquina ${machineName} eliminada`, 'info');
            }
        }

        // Limpiar todo el historial
        function clearAll() {
            if (confirm('¿Eliminar TODOS los registros de producción?')) {
                productionHistory = [];
                localStorage.removeItem('productionHistory');
                updateHistoryGrid();
                updatePalletTracker();
                updateTotals();
                showMessage('🗑️ Todos los registros eliminados', 'info');
            }
        }

        // Actualizar totales
        function updateTotals() {
            try {
                const today = new Date().toLocaleDateString('es-ES');
                const todayRecords = productionHistory.filter(r => r.date === today);
                
                const totalPieces = todayRecords.reduce((sum, r) => sum + r.piecesPerShift, 0);
                const totalPallets = todayRecords.reduce((sum, r) => sum + r.palletsPerShift, 0);
                const totalScrap = todayRecords.reduce((sum, r) => sum + r.scrapPerShift, 0);
                const uniqueMachines = [...new Set(todayRecords.map(r => r.machine))].length;
                
                // Calcular producción por hora promedio
                let avgHourly = 0;
                if (todayRecords.length > 0) {
                    const totalHourly = todayRecords.reduce((sum, r) => sum + r.realHourly, 0);
                    avgHourly = Math.round(totalHourly / todayRecords.length);
                }
                
                // Actualizar UI
                document.getElementById('totalTodayPieces').textContent = totalPieces.toLocaleString();
                document.getElementById('totalTodayPallets').textContent = totalPallets;
                document.getElementById('totalTodayScrap').textContent = totalScrap + ' pz';
                document.getElementById('machinesToday').textContent = uniqueMachines;
                document.getElementById('avgHourlyToday').textContent = avgHourly + ' pz';
                
            } catch (error) {
                console.error('Error actualizando totales:', error);
            }
        }

        // Cargar datos de ejemplo
        function loadSampleData() {
            try {
                const now = new Date().toLocaleDateString('es-ES');
                const sampleData = [
                    {
                        id: 1,
                        time: '08:30',
                        date: now,
                        machine: 'M-001',
                        product: 'Caja Plástica 250ml',
                        cycleInit: 2.4,
                        cycleFinal: 2.2,
                        cavities: 8,
                        piecesPerShift: 8392,
                        boxesPerShift: 83,
                        palletsPerShift: 1,
                        scrapPerHour: 31,
                        scrapPerShift: 248,
                        theoreticalHourly: 1069,
                        realHourly: 1049,
                        cyclesPerHour: 1500,
                        avgCycle: 2.3,
                        piecesPerBox: 100,
                        boxesPerPallet: 50,
                        shiftHours: 8,
                        timePerPallet: '8h',
                        operator: 'OP3'
                    },
                    {
                        id: 2,
                        time: '10:15',
                        date: now,
                        machine: 'M-002',
                        product: 'Tuerca M8',
                        cycleInit: 1.8,
                        cycleFinal: 1.6,
                        cavities: 16,
                        piecesPerShift: 25600,
                        boxesPerShift: 256,
                        palletsPerShift: 5,
                        scrapPerHour: 79,
                        scrapPerShift: 632,
                        theoreticalHourly: 2635,
                        realHourly: 2556,
                        cyclesPerHour: 2000,
                        avgCycle: 1.7,
                        piecesPerBox: 100,
                        boxesPerPallet: 50,
                        shiftHours: 8,
                        timePerPallet: '1h 36m',
                        operator: 'OP5'
                    },
                    {
                        id: 3,
                        time: '12:45',
                        date: now,
                        machine: 'M-003',
                        product: 'Arandela Plana',
                        cycleInit: 3.2,
                        cycleFinal: 3.0,
                        cavities: 4,
                        piecesPerShift: 2880,
                        boxesPerShift: 28,
                        palletsPerShift: 1,
                        scrapPerHour: 11,
                        scrapPerShift: 88,
                        theoreticalHourly: 366,
                        realHourly: 355,
                        cyclesPerHour: 1125,
                        avgCycle: 3.1,
                        piecesPerBox: 100,
                        boxesPerPallet: 50,
                        shiftHours: 8,
                        timePerPallet: '8h',
                        operator: 'OP7'
                    }
                ];
                
                productionHistory = sampleData;
                localStorage.setItem('productionHistory', JSON.stringify(productionHistory));
                updateHistoryGrid();
                updatePalletTracker();
                updateTotals();
                showMessage('📊 Datos de ejemplo cargados', 'info');
            } catch (error) {
                console.error('Error cargando datos de ejemplo:', error);
            }
        }

        // Exportar datos
        function exportData() {
            try {
                const today = new Date().toLocaleDateString('es-ES');
                const todayRecords = productionHistory.filter(r => r.date === today);
                
                if (todayRecords.length === 0) {
                    showMessage('📭 No hay datos para exportar', 'warning');
                    return;
                }
                
                const csv = [
                    ['Hora', 'Máquina', 'Producto', 'Ciclo Inic', 'Ciclo Fin', 'Cav', 'Pz Teórica/h', 'Pz Real/h', 'Scrap/h', 'Pz Turno', 'Cajas', 'Tarimas', 'Tiempo/Tarima', 'Scrap Turno', 'Operador', 'Prod/Hora'],
                    ...todayRecords.map(r => [
                        r.time, r.machine, r.product, r.cycleInit.toFixed(2), r.cycleFinal.toFixed(2), r.cavities,
                        r.theoreticalHourly, r.realHourly, r.scrapPerHour,
                        r.piecesPerShift, r.boxesPerShift, r.palletsPerShift, r.timePerPallet, r.scrapPerShift, r.operator, r.realHourly
                    ])
                ].map(row => row.join(',')).join('\n');
                
                const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `produccion_scrap_${today.replace(/\//g, '-')}.csv`;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                URL.revokeObjectURL(url);
                
                showMessage('📊 Datos exportados a CSV', 'success');
            } catch (error) {
                console.error('Error exportando:', error);
                showMessage('❌ Error al exportar datos', 'error');
            }
        }

        // Cerrar turno y generar PDF
        function closeShift() {
            try {
                if (!confirm('¿Cerrar turno y generar reporte?')) return;
                
                const today = new Date().toLocaleDateString('es-ES');
                const shiftRecords = productionHistory.filter(r => r.date === today);
                
                if (shiftRecords.length === 0) {
                    showMessage('📭 No hay producción registrada hoy', 'warning');
                    return;
                }
                
                // Generar PDF
                const { jsPDF } = window.jspdf;
                const doc = new jsPDF();
                
                doc.setFontSize(16);
                doc.text('REPORTE DE PRODUCCIÓN CON SCRAP', 105, 20, { align: 'center' });
                
                doc.setFontSize(12);
                doc.text(`Fecha: ${today}`, 20, 35);
                doc.text(`Scrap aplicado: ${scrapPercentage}%`, 20, 42);
                doc.text(`Producción promedio por hora: ${Math.round(shiftRecords.reduce((s, r) => s + r.realHourly, 0) / shiftRecords.length)} pz`, 20, 49);
                
                let y = 65;
                doc.setFontSize(14);
                doc.text('Resumen por Máquina', 20, y);
                y += 10;
                
                doc.setFontSize(10);
                shiftRecords.forEach((record, i) => {
                    if (y > 270) {
                        doc.addPage();
                        y = 20;
                    }
                    
                    doc.text(`${record.machine} - ${record.product}`, 20, y);
                    doc.text(`Prod/h: ${record.realHourly} pz | Scrap/h: ${record.scrapPerHour} pz`, 120, y);
                    y += 7;
                });
                
                doc.save(`produccion_scrap_${today.replace(/\//g, '-')}.pdf`);
                
                showMessage('✅ Reporte PDF generado', 'success');
            } catch (error) {
                console.error('Error generando PDF:', error);
                showMessage('❌ Error al generar PDF', 'error');
            }
        }

        // Mostrar mensaje temporal
        function showMessage(message, type = 'info') {
            const colors = {
                success: '#10b981',
                error: '#ef4444',
                warning: '#f59e0b',
                info: '#3b82f6'
            };
            
            const msg = document.createElement('div');
            msg.style.cssText = `
                position: fixed;
                bottom: 20px;
                right: 20px;
                background: ${colors[type] || colors.info};
                color: white;
                padding: 12px 18px;
                border-radius: 8px;
                box-shadow: 0 4px 12px rgba(0,0,0,0.15);
                z-index: 1000;
                font-size: 14px;
                font-weight: 500;
                animation: slideIn 0.3s ease;
                display: flex;
                align-items: center;
                gap: 8px;
                max-width: 300px;
            `;
            msg.innerHTML = message;
            document.body.appendChild(msg);
            
            setTimeout(() => {
                msg.style.animation = 'fadeOut 0.3s ease';
                setTimeout(() => {
                    if (msg.parentNode) {
                        msg.parentNode.removeChild(msg);
                    }
                }, 300);
            }, 3000);
        }

        // Actualizar cada minuto
        setInterval(updatePalletTracker, 60000);
        
        // =============================================
        // INICIALIZACIÓN
        // =============================================
        document.addEventListener('DOMContentLoaded', function() {
            checkLoginStatus();
        });
    </script>

    <!-- Estilos para animaciones -->
    <style>
        @keyframes slideIn {
            from { transform: translateX(100%); opacity: 0; }
            to { transform: translateX(0); opacity: 1; }
        }
        
        @keyframes fadeOut {
            from { opacity: 1; }
            to { opacity: 0; transform: translateX(100%); }
        }
    </style>
</body>
</html>
