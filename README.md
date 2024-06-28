```rust

use serde_json::Value;
use tao::{
    event_loop::EventLoopProxy,
    window::{Window, WindowId},
};
use wry::{
    WebViewBuilder,
    WebView,
};
use serde_json::Value;
use tao::{
    dpi::{LogicalSize, PhysicalPosition}, 
    event_loop::EventLoop, 
    window::WindowBuilder
};


fn get_value_from_json(json_data: &str, key: &str) -> Option<Value> {
    let parsed_json: Value = serde_json::from_str(json_data).expect("Invalid JSON data");
    match parsed_json {
        Value::Object(map) => map.get(key).cloned(),
        _ => None,
    }
}

pub fn apply_window_builder_option (
    mut builder: WindowBuilder ,
    key: &str,
    value: &Value,
    event_loop: EventLoop<()>,
) -> WindowBuilder {
    match key {
        "with_always_on_bottom" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_always_on_bottom(val);
            }
        }
        "with_always_on_top" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_always_on_top(val);
            }
        }
        "with_closable" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_closable(val);
            }
        }
        "with_content_protection" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_content_protection(val);
            }
        }
        "with_decorations" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_decorations(val);
            }
        }
        "with_focused" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_focused(val);
            }
        }
        "with_fullscreen" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_fullscreen(val);
            }
        }
        "with_inner_size" => {
            if let Some(obj) = value.as_object() {
                if let (Some(width), Some(height)) = (obj.get("width"), obj.get("height")) {
                    if let (Some(w), Some(h)) = (width.as_f64(), height.as_f64()) {
                        builder = builder.with_inner_size(LogicalSize::new(w, h));
                    }
                }
            }
        }
        "with_inner_size_constraints" => {
            if let Some(obj) = value.as_object() {
                let min_width = obj.get("min_width").and_then(|v| v.as_f64()).unwrap_or(0.0);
                let min_height = obj.get("min_height").and_then(|v| v.as_f64()).unwrap_or(0.0);
                let max_width = obj.get("max_width").and_then(|v| v.as_f64()).unwrap_or(f64::INFINITY);
                let max_height = obj.get("max_height").and_then(|v| v.as_f64()).unwrap_or(f64::INFINITY);
                builder = builder.with_inner_size_constraints(LogicalSize::new(min_width, min_height));
            }
        }
        "with_max_inner_size" => {
            if let Some(obj) = value.as_object() {
                if let (Some(width), Some(height)) = (obj.get("width"), obj.get("height")) {
                    if let (Some(w), Some(h)) = (width.as_f64(), height.as_f64()) {
                        builder = builder.with_max_inner_size(LogicalSize::new(w, h));
                    }
                }
            }
        }
        "with_maximizable" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_maximizable(val);
            }
        }
        "with_maximized" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_maximized(val);
            }
        }
        "with_min_inner_size" => {
            if let Some(obj) = value.as_object() {
                if let (Some(width), Some(height)) = (obj.get("width"), obj.get("height")) {
                    if let (Some(w), Some(h)) = (width.as_f64(), height.as_f64()) {
                        builder = builder.with_min_inner_size(LogicalSize::new(w, h));
                    }
                }
            }
        }
        "with_minimizable" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_minimizable(val);
            }
        }
        "with_position" => {
            if let Some(obj) = value.as_object() {
                if let (Some(x), Some(y)) = (obj.get("x"), obj.get("y")) {
                    if let (Some(x), Some(y)) = (x.as_f64(), y.as_f64()) {
                        builder = builder.with_position(PhysicalPosition::new(x, y));
                    }
                }
            }
        }
        "with_resizable" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_resizable(val);
            }
        }
        "with_theme" => {
            if let Some(val) = value.as_str() {
                let theme = match val {
                    "light" => tao::window::Theme::Light,
                    "dark" => tao::window::Theme::Dark,
                    _ => tao::window::Theme,
                };
                builder = builder.with_theme(Some(theme));
            }
        }
        "with_title" => {
            if let Some(val) = value.as_str() {
                builder = builder.with_title(val);
            }
        }
        "with_transparent" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_transparent(val);
            }
        }
        "with_visible" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_visible(val);
            }
        }
        "with_visible_on_all_workspaces" => {
            if let Some(val) = value.as_bool() {
                builder = builder.with_visible_on_all_workspaces(val);
            }
        }
        "with_window_icon" => {
            if let Some(obj) = value.as_object() {
                if let Some(path) = obj.get("path") {
                    if let Some(path) = path.as_str() {
                        let path_buf = PathBuf::from(path);
                        if let Ok(icon) = Icon::from_path(path_buf) {
                           builder = builder.with_window_icon(Some(icon));
                        }
                    }
                }
            }
        }
        _ => {}
    }
    builder.build(&event_loop);
}

pub fn recursive_window_initialization_through_JSON_objects(
    event_loop:&EventLoop<()>,
    json_data: &str,
) -> WindowBuilder {
    let mut builder = WindowBuilder::new();

    let parsed_json: Value = serde_json::from_str(json_data).expect("Invalid JSON data");

    if let Value::Object(map) = parsed_json {
        for (key, value) in map {
            builder = apply_window_builder_option(builder, &key, &value, event_loop);
        }
    }

    builder
}






pub fn apply_webview_builder_option(
    key: &str,
    value: &Value,
    window: &Window,
) -> WebViewBuilder {
    let builder = WebViewBuilder::new(window);
    match key {
        "with_accept_first_mouse" => {
            if let Some(val) = value.as_bool() {
                builder.with_accept_first_mouse(val)
            } else {
                println!("with_accept_first_mouse expects a boolean");
                builder
            }
        }
        "with_asynchronous_custom_protocol" => {
            if let Some(val) = value.as_str() {
                // builder.with_asynchronous_custom_protocol(val.to_string(), |_request| {
                    // Implement the custom protocol handling here
                    // Box::pin(async { Ok((vec![], "application/json".to_string())) })
                // })
            } else {
                println!("with_asynchronous_custom_protocol expects a string");
                builder
            }
        }
        "with_autoplay" => {
            if let Some(val) = value.as_bool() {
                builder.with_autoplay(val)
            } else {
                println!("with_autoplay expects a boolean");
                builder
            }
        }
        "with_back_forward_navigation_gestures" => {
            if let Some(val) = value.as_bool() {
                builder.with_back_forward_navigation_gestures(val)
            } else {
                println!("with_back_forward_navigation_gestures expects a boolean");
                builder
            }
        }
        "with_background_color" => {
            if let Some(val) = value.as_str() {
                // Assuming color is provided as hex string
                // let color:VierByte = val.parse().unwrap_or(0xFFFFFF); // Default to white
                // builder.with_background_color(color)
            } else {
                println!("with_background_color expects a string");
                builder
            }
        }
        "with_bounds" => {
            if let Some(val) = value.as_str() {
                // builder.with_bounds(val)
            } else {
                println!("with_bounds expects a string");
                builder
            }
        }
        "with_clipboard" => {
            if let Some(val) = value.as_bool() {
                builder.with_clipboard(val)
            } else {
                println!("with_clipboard expects a string");
                builder
            }
        }
        "with_custom_protocol" => {
            if let Some(val) = value.as_str() {
                // builder.with_custom_protocol(val.to_string(), |_request| {
                    // Implement the custom protocol handling here
                    // Box::pin(async { Ok((vec![], "application/json".to_string())) })
                // })
            } else {
                println!("with_custom_protocol expects a string");
                builder
            }
        }
        "with_devtools" => {
            if let Some(val) = value.as_bool() {
                builder.with_devtools(val)
            } else {
                println!("with_devtools expects a boolean");
                builder
            }
        }
        "with_document_title_changed_handler" => {
            if let Some(val) = value.as_str() {
                // builder.with_document_title_changed_handler(val)
            } else {
                println!("with_document_title_changed_handler expects a string");
                builder
            }
        }
        "with_download_completed_handler" => {
            if let Some(val) = value.as_str() {
                // builder.with_download_completed_handler(val)
            } else {
                println!("with_download_completed_handler expects a string");
                builder
            }
        }
        "with_download_started_handler" => {
            if let Some(val) = value.as_str() {
                // builder.with_download_started_handler(val)
            } else {
                println!("with_download_started_handler expects a string");
                builder
            }
        }
        "with_drag_drop_handler" => {
            if let Some(val) = value.as_str() {
                // builder.with_drag_drop_handler(val)
            } else {
                println!("with_drag_drop_handler expects a string");
                builder
            }
        }
        "with_focused" => {
            if let Some(val) = value.as_bool() {
                builder.with_focused(val)
            } else {
                println!("with_focused expects a boolean");
                builder
            }
        }
        "with_headers" => {
            if let Some(val) = value.as_str() {
                // builder.with_headers(val)
            } else {
                println!("with_headers expects a string");
                builder
            }
        }
        "with_hotkeys_zoom" => {
            if let Some(val) = value.as_bool() {
                builder.with_hotkeys_zoom(val)
            } else {
                println!("with_hotkeys_zoom expects a boolean");
                builder
            }
        }
        "with_incognito" => {
            if let Some(val) = value.as_bool() {
                builder.with_incognito(val)
            } else {
                println!("with_incognito expects a boolean");
                builder
            }
        }
        "with_html" => {
            if let Some(val) = value.as_str() {
                builder.with_html(val)
            } else {
                println!("with_html expects a string");
                builder
            }
        }
        "with_initialization_script" => {
            if let Some(val) = value.as_str() {
                builder.with_initialization_script(val)
            } else {
                println!("with_initialization_script expects a string");
                builder
            }
        }
        "with_ipc_handler" => {
            if let Some(val) = value.as_str() {
                // builder.with_ipc_handler(val)
            } else {
                println!("with_ipc_handler expects a string");
                builder
            }
        }
        "with_navigation_handler" => {
            if let Some(val) = value.as_str() {
               // builder.with_navigation_handler(val)
            } else {
                println!("with_navigation_handler expects a string");
                builder
            }
        }
        "with_new_window_req_handler" => {
            if let Some(val) = value.as_str() {
               // builder.with_new_window_req_handler(val)
            } else {
                println!("with_new_window_req_handler expects a string");
                builder
            }
        }
        "with_proxy_config" => {
            if let Some(val) = value.as_str() {
                // builder.with_proxy_config(val)
            } else {
                println!("with_proxy_config expects a string");
                builder
            }
        }
        "with_on_page_load_handler" => {
            if let Some(val) = value.as_str() {
                // builder.with_on_page_load_handler(val)
            } else {
                println!("with_on_page_load_handler expects a string");
                builder
            }
        }
        "with_transparent" => {
            if let Some(val) = value.as_bool() {
                builder.with_transparent(val)
            } else {
                println!("with_transparent expects a boolean");
                builder
            }
        }
        "with_url" => {
            if let Some(val) = value.as_str() {
                builder.with_url(val)
            } else {
                println!("with_url expects a string");
                builder
            }
        }
        "with_url_and_headers" => {
            if let Some(val) = value.as_str() {
                // builder.with_url_and_headers(val)
            } else {
                println!("with_url_and_headers expects a string");
                builder
            }
        }
        "with_user_agent" => {
            if let Some(val) = value.as_str() {
                builder.with_user_agent(val)
            } else {
                println!("with_user_agent expects a string");
                builder
            }
        }
        "with_visible" => {
            if let Some(val) = value.as_bool() {
                builder.with_visible(val)
            } else {
                println!("with_visible expects a boolean");
                builder
            }
        }
        "with_web_context" => {
            if let Some(val) = value.as_str() {
                // builder.with_web_context(val)
            } else {
                println!("with_web_context expects a string");
                builder
            }
        }
        _ => builder,
    }
}

pub fn recursive_webview_initialization_through_JSON_objects(
    window: &Window,
    json_data: &str,
) -> WebView {
    let mut builder = WebViewBuilder::new(&window);

    let parsed_json: Value = serde_json::from_str(json_data).expect("Invalid JSON data");

    if let Value::Object(map) = parsed_json {
        for (key, value) in map {
            builder = apply_webview_builder_option(builder, &key, &value);
        }
    }

    builder.build().unwrap()
}








mod utils;
mod webview;
mod window;


use tao::event_loop::EventLoop;

use  crate::window::recursive_window_initialization_through_JSON_objects;
use crate::webview::recursive_webview_initialization_through_JSON_objects;



fn main() {
    let event_loop = EventLoop::new();
    let window_data = r#"
    {
        "with_title": "My Window",
        "with_resizable": true,
        "with_inner_size": {
            "width": 800,
            "height": 600
        },
        "with_position": {
            "x": 100,
            "y": 100
        },
        "with_theme": "dark",
        "with_window_icon": {
            "path": "icon.png"
        }
    }
    "#;
    let webview_data = r#"
    {
        "with_url": "https://www.example.com",
        "with_devtools": true,
        "with_autoplay": false,
        "with_transparent": true
    }
    "#;

    let window = recursive_window_initialization_through_JSON_objects(event_loop, window_data);
    let webview = recursive_webview_initialization_through_JSON_objects(&window, webview_data);

    event_loop.run(move |event, _, control_flow| {
        *control_flow = tao::event_loop::ControlFlow::Wait;
        match event {
            _ => ()
        }
    });
}




```
