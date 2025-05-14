import { useEffect, useState, ComponentType } from "react"

// Full tutorial on https://framermart.com

// List of headings to display
const HEADING_TO_DISPLAY = ["h1", "h2", "h3", "h4", "h5", "h6"]

export function withTableOfContent(Component: ComponentType): ComponentType {
    return (props) => {
        const [headings, setHeadings] = useState<
            Array<{ text: string; href: string; type: string }>
        >([])

        useEffect(() => {
            const newHeadings: Array<{
                text: string
                href: string
                type: string
            }> = []
            // Get all headings with an anchor tag
            document
                .querySelectorAll("h1 a, h2 a, h3 a, h4 a, h5 a, h6 a")
                .forEach((anchor) => {
                    const headingElement = anchor as HTMLAnchorElement
                    const headingTag =
                        headingElement.parentElement?.tagName.toLowerCase()
                    newHeadings.push({
                        text: headingElement.textContent || "",
                        href: headingElement.href,
                        type: headingTag || "",
                    })
                })

            // Filter out headings that are not in the list of headings to display and do not have an href that includes "#"
            setHeadings(
                newHeadings.filter(
                    ({ type, href }) =>
                        HEADING_TO_DISPLAY.includes(type) && href.includes("#")
                )
            )
        }, [])

        return (
            <>
                {headings.length > 0 ? (
                    headings.map(({ text, href, type }) => (
                        <Component
                            {...props}
                            // @ts-ignore
                            variant={type}
                            title={text}
                            link={href}
                        />
                    ))
                ) : (
                    <Component {...props} />
                )}
            </>
        )
    }
}
