# Apply_the_filter_on_image
Enter the image path , Apply the filter and then display it. Image can be downloaded.


    from tkinter import messagebox
    import tkinter as tk
    from PIL import Image, ImageTk
    import cv2 as cv
    from tkinter import messagebox
    root=Tk()
    root.geometry("400x570")
    root.title("Apply the Filter on image")
    root.maxsize(400,570)
    root.configure(bg='black')
    filter_img=""
    def Save(event):
        # Save the downloaded image to a local file
        local_filename = "captured_image.png"
        cv2.imwrite(local_filename, filter_img)
        print(f"Image saved as {local_filename}")
        messagebox.showinfo("Capture", "Image saved as 'captured_image.png'")
    def click(event):
        global scvalue,variable,filter_img
        text1=scvalue.get()
        text2=variable.get()
        image=cv.imread(text1)
        if text2=='Delight':
            filter_img = cv2.convertScaleAbs(image, alpha=1.5, beta=50)
        elif text2=="Crystal":
            # Apply Gaussian blur
            blurred = cv2.GaussianBlur(image, (15, 15), 0)
            # Apply Laplacian edge detection
            edges = cv2.Laplacian(blurred, cv2.CV_8U)  # Convert to 8-bit format
            # Convert edges to grayscale
            edges = cv2.cvtColor(edges, cv2.COLOR_BGR2GRAY)
            # Invert the edges
            edges = cv2.bitwise_not(edges)
            # Create a mask of the inverted edges
            mask = cv2.bitwise_not(edges)
            # Create a crystal-like effect by applying the mask to the original image
            filter_img = cv2.bitwise_and(image, image, mask=mask)
        elif text2=='Misty':
            # Convert the image to a gray scale image
            gray_image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        
            # Apply Gaussian blur to the gray scale image
            blurred_image = cv2.GaussianBlur(gray_image, (0, 0), 10)
        
            # Create a misty effect by blending the original image and the blurred image
            filter_img= cv2.addWeighted(image, 0.5, cv2.cvtColor(blurred_image, cv2.COLOR_GRAY2BGR), 1 - 0.5, 0)
        elif text2=='Warm':
            # Scale the individual color channels
            warm_image = image.copy()
            warm_image[:, :, 2] = cv2.add(warm_image[:, :, 2], (1.3 - 1) * 255)
            warm_image[:, :, 0] = cv2.add(warm_image[:, :, 0], (0.7 - 1) * 255)
            warm_image[:, :, 1] = cv2.add(warm_image[:, :, 1], (0.9 - 1) * 255)
            # Clip the values to ensure they are within the valid range [0, 255]
            filter_img = np.clip(warm_image, 0, 255)
        elif text2=='Candy':
            gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
            # Apply bilateral filter for smoothing while preserving edges
            smoothed = cv2.bilateralFilter(image, 9, 75, 75) 
            # Perform edge detection
            edges = cv2.Canny(smoothed, 100, 200)
            # Create a cartoon effect by bitwise AND of the grayscale image and edges
            filter_img = cv2.bitwise_and(smoothed, smoothed, mask=edges)
        elif text2=='Moonrise':
            # Convert the image to grayscale
            gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        
            # Apply a blueish tint to the image
            blue_tinted = cv2.applyColorMap(gray, cv2.COLORMAP_WINTER)
            filter_img = cv2.convertScaleAbs(blue_tinted, alpha=1.2, beta=20)
        elif text2=="Modern Gold":
            gold_image = np.zeros_like(image)
            gold_image[:] = (0, 215, 255)
            filter_img = cv2.addWeighted(image, 0.7, gold_image, 0.3, 0)
        elif text2=='Cyberpunk':
            # Increase the red and blue channels to emphasize colors
            image[:, :, 0] += 100  # Increase blue channel
            image[:, :, 2] += 100  # Increase red channel
            # Apply a vignette effect
            height, width, _ = image.shape
            y, x = np.ogrid[:height, :width]
            mask = ((x - width / 2) ** 2 + (y - height / 2) ** 2) > (width * 0.4) ** 2
            # Convert the image to float32 before applying the operation
            image = image.astype('float32')
            image[mask] *= 0.7  # Darken the pixels outside the vignette
            # Convert the image back to uint8
            image = image.astype('uint8')
            filter_img = cv2.convertScaleAbs(image, alpha=1.5, beta=25)
        elif text2=="B&W":
            filter_img = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        elif text2=="HSV":
            filter_img=cv.cvtColor(image,cv.COLOR_BGR2HSV)
        output_file = 'output.jpg'
        cv2.imwrite(output_file, filter_img)
        show_image()
        cv.waitKey(0)
    
    def show_image():
        f2=Frame(root)
        # Load the image
        img = Image.open("output.jpg")
        resized_image = img.resize((300, 200))
    
        # Create a PhotoImage object and keep a reference to it
        global photo
        photo = ImageTk.PhotoImage(resized_image)
    
        # Create a label to display the resized image
        label = tk.Label(f2, image=photo)
        label.pack()
        f2.pack(pady=30)
    f1=Frame(root)    
    text_label = tk.Label(f1, text="Enter the image path (.jpg)",fg="White",bg="Black")
    text_label.pack(side="left")
    f1.pack(pady=50)
    scvalue=StringVar()
    scvalue.set("")
    screen=Entry(root, textvar=scvalue,font="lucida 15 bold")
    screen.pack(fill=X,padx=15)
    
    filter={"Delight","Crystal","Misty","Warm","Candy","Moonrise","Modern Gold","Cyberpunk","B&W","HSV"}
    variable=StringVar(root)
    variable.set("Filter")
    drop=OptionMenu(root,variable,*filter)
    drop.pack(fill=X,ipadx=70,pady=40,padx=15)
    
    f=Frame(root)
    filter=Button(f,text="Select",font=small_font)
    filter.pack(side=LEFT)
    filter.bind("<Button-1>",click)
    download=Button(f,text="Download",font=small_font)
    download.pack(side=LEFT)
    download.bind("<Button-1>",Save)
    f.pack()
    root.mainloop()
