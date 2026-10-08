<script lang="ts">
    type FormItem = {
        id: string;
        name: string;
        value: string;
        message: string;
        status: "idle" | "error" | "success";
    };

    type FormItems = Record<"username" | "email" | "password" | "confirmPassword", FormItem>;

    let form_items = $state<FormItems>({
        username: { id: "username", name: "Username", value: "", message: "", status: "idle" },
        email: { id: "email", name: "Email", value: "", message: "", status: "idle" },
        password: { id: "password", name: "Password", value: "", message: "", status: "idle" },
        confirmPassword: { id: "re-password", name: "Confirm Password", value: "", message: "", status: "idle" }
    });

    function showError(item: FormItem, message: string): void {
        if(message == "")
            item.message = message;
        item.status = "error";
    }

    function showSuccess(item: FormItem): void {
        item.message = "";
        item.status = "success";
    }

    function resetInputClass(item: FormItem): void {
        item.value = "";
        item.message = "";
        item.status = "idle";
    }

    function checkRequired(items: FormItem[]): boolean {
        let isValid = true;

        items.forEach((item) => {
            if (item.value.trim() === "") {
                isValid = false;
                showError(item, `${item.name} is required`);
            }
        });
        return isValid;
    }
    
    function checkLength(item: FormItem, min: number, max: number): boolean {
        if (item.value.length < min) {
            showError(item, `${item.name} must be at least ${min} characters.`);
            return false;
        } else if (item.value.length > max) {
            showError(item, `${item.name} must be less than ${max} characters.`);
            return false;
        } else {
            return true;
        }
    }

    function checkPasswordsMatch(password: FormItem, confirmPassword: FormItem): boolean {
        if (password.value !== confirmPassword.value) {
            showError(confirmPassword, "Passwords do not match");
            return false;
        }
        return true;
    }

    function checkEmail(email: FormItem): boolean {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        if (emailRegex.test(email.value.trim())) {
            return true;
        } else {
            showError(email, "Email is not valid");
            return false;
        }
    }


    function onsubmit(ev: SubmitEvent): void
    {
        ev.preventDefault();
        
        let isFormValid = false;
        
        const { username, email, password, confirmPassword } = form_items;
        const isRequiredValid = checkRequired(Object.values(form_items))
        const isUsernameValid = checkLength(username, 3, 15);
        const isEmailValid = checkEmail(email);
        const isPasswordValid = checkLength(password, 6, 25);
        const isPasswordsMatch = checkPasswordsMatch(password, confirmPassword);

        isFormValid = isRequiredValid && isUsernameValid && isEmailValid && isPasswordValid && isPasswordsMatch;
        if (isFormValid) {
            alert("Registration successful!");
            Object.values(form_items).forEach(resetInputClass);
        }
        else
        {
            Object.values(form_items).forEach((item) => {
                if(item.status === "idle")
                    item.status = "success";
            })
        }
    }
</script>

<div class="container">
    <form class="registration-form" {onsubmit}>
        <h1>Register an account</h1>
        {#each Object.values(form_items) as item}
            <div class="form-item">
                <label for={item.id}>{item.name}</label>
                <input
                    id={item.id}
                    type="text"
                    placeholder={item.name}
                    bind:value={item.value}
                    class="{item.status}"
                />
                <small class="{item.status}">{item.message}</small>
            </div>
        {/each}
        <button type="submit" class="register-btn">Register!!!</button>
    </form>
</div>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

.container {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0px 5px 15px rgba(0,0,0,0.1);
    max-width: 400px;
    width: 100%;
    padding: 30px;
    overflow: hidden;
}

h1 {
    text-align: center;
    margin-bottom: 1.2rem;
}

.form-item {
    margin-bottom: 20px;
    position: relative;
}

.form-item label {
    display: block;
    margin-bottom: 8px;
    font-weight: 600;
    color: #333;
}

.form-item input.idle {
    width: 100%;
    padding: 12px 14px;
    border: 1px solid #ddd;
    border-radius: 6px;
    font-size: 1rem;
    transition: border-color 0.2s ease, box-shadow 0.2s ease;
}

.form-item input.error {
    width: 100%;
    padding: 12px 14px;
    border: 1px solid red;
    border-radius: 6px;
    font-size: 1rem;
    transition: border-color 0.2s ease, box-shadow 0.2s ease;
}

.form-item input.success {
    width: 100%;
    padding: 12px 14px;
    border: 1px solid green;
    border-radius: 6px;
    font-size: 1rem;
    transition: border-color 0.2s ease, box-shadow 0.2s ease;
}

.form-item input:focus {
    outline: none;
    border-color: #4a90e2;
    box-shadow: 0 0 0 3px rgba(74, 144, 226, 0.12);
}

.form-item small {
    display: block;
    min-height: 18px;
    margin-top: 6px;
    color: #e74c3c;
    font-size: 0.75rem;
}

.register-btn {
    width: 100%;
    padding: 12px 16px;
    border: none;
    border-radius: 6px;
    background-color: #3b82f6;
    color: white;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: background-color 0.2s ease;
}

.register-btn:hover {
    background-color: #2563eb;
}

</style>
