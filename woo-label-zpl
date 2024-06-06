<?php
/**
 * Plugin Name: WooCommerce Zebra Labels
 * Plugin URI:  http://example.com
 * Description: Automatically generates Zebra labels for each WooCommerce order.
 * Version:     1.0
 * Author:      Tu Nombre
 * Author URI:  http://example.com
 */

if (!defined('ABSPATH')) {
    exit; // Exit if accessed directly
}

// Añadir botón para imprimir etiqueta en el listado de órdenes
add_action('woocommerce_admin_order_actions_end', 'add_zebra_label_button');

function add_zebra_label_button($order) {
    echo '<a href="' . wp_nonce_url(admin_url('admin-ajax.php?action=print_zebra_label&order_id=' . $order->get_id()), 'print-zebra-label') . '" class="button tips zebra-labels" data-tip="Imprimir etiqueta Zebra" target="_blank">Imprimir Etiqueta</a>';
}

// Manejar la solicitud de impresión de etiquetas
add_action('wp_ajax_print_zebra_label', 'handle_print_zebra_label');

function handle_print_zebra_label() {
    if (!current_user_can('manage_woocommerce') || !wp_verify_nonce($_REQUEST['_wpnonce'], 'print-zebra-label')) {
        wp_die('No tienes permiso para imprimir esta etiqueta.');
    }

    $order_id = isset($_GET['order_id']) ? intval($_GET['order_id']) : 0;
    if ($order_id) {
        $order = wc_get_order($order_id);
        generate_zebra_label($order);
    }
    exit;
}

function generate_zebra_label($order) {
    $order_number = $order->get_order_number();
    $first_name = $order->get_billing_first_name();
    $last_name = $order->get_billing_last_name();
    $company = $order->get_billing_company();
    $city = $order->get_billing_city();
    $state = $order->get_billing_state();
    $phone = $order->get_billing_phone();

    $zpl = "^XA
            ^CF0,40
            ^FO30,20^FDN de Orden: $order_number^FS
            ^FO30,70^FDNombre: $first_name^FS
            ^FO30,120^FDApellido: $last_name^FS";

    if (!empty($company)) {
        $zpl .= "^FO30,170^FDEmpresa: $company^FS";
        $offset = 220;
    } else {
        $offset = 170;
    }

    $zpl .= "^FO30,$offset^FDCiudad: $city^FS
             ^FO30," . ($offset + 50) . "^FDRegion: $state^FS
             ^FO30," . ($offset + 100) . "^FD^FS
             ^FO30," . ($offset + 150) . "^FDTelefono: $phone^FS
             ^XZ";

    echo "<pre>" . htmlspecialchars($zpl) . "</pre>";
}

function send_to_printer($zpl) {
    // Implementación específica según tu impresora y entorno de red
}
